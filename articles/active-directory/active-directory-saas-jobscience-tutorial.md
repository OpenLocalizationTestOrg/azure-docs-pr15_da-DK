<properties 
    pageTitle="Selvstudium: Azure Active Directory-integration med Jobscience | Microsoft Azure" 
    description="Lær, hvordan du bruger Jobscience med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!" 
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

#<a name="tutorial-azure-active-directory-integration-with-jobscience"></a>Selvstudium: Azure Active Directory-integration med Jobscience
  
Formålet med dette selvstudium er at vise integration af Azure og Jobscience.  
Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   Et Jobscience Single Sign-On aktiveret abonnement
  
Når du har fuldført dette selvstudium, vil de Azure AD-brugere, du har tildelt til Jobscience kunne enkelt Log på programmet på webstedet Jobscience virksomhed (service provider startet sign-on) eller ved hjælp af [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md).
  
Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integrationen for Jobscience
2.  Konfigurere Enkeltlogon
3.  Konfiguration af brugerklargøring af
4.  Tildele brugere

![Scenarie] (./media/active-directory-saas-jobscience-tutorial/IC784341.png "Scenarie")
##<a name="enabling-the-application-integration-for-jobscience"></a>Aktivere programmet integrationen for Jobscience
  
Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integrationen for Jobscience.

###<a name="to-enable-the-application-integration-for-jobscience-perform-the-following-steps"></a>For at aktivere programmet integration for Jobscience skal du udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-jobscience-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-jobscience-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-jobscience-tutorial/IC749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-jobscience-tutorial/IC749322.png "Tilføj et program fra gallerry")

6.  I **søgefeltet**skal du skrive **jobscience**.

    ![Galleri med programmet] (./media/active-directory-saas-jobscience-tutorial/IC784342.png "Galleri med programmet")

7.  Vælg **Jobscience**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![Jobscience] (./media/active-directory-saas-jobscience-tutorial/IC784357.png "Jobscience")
##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon
  
Formålet med dette afsnit er til at disponere hvordan du aktiverer brugere til at godkende Jobscience med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen.  
Konfiguration af single sign-on til Jobscience, skal du hente en miniatureværdi fra et certifikat.  
Hvis du ikke kender med denne procedure, se, [hvordan du hente et certifikat miniature værdi](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Log på webstedet Jobscience virksomhed som administrator.

2.  Gå til **konfiguration**.

    ![Konfiguration] (./media/active-directory-saas-jobscience-tutorial/IC784358.png "Konfiguration")

3.  Klik på **Domain Management** for at udvide sektionen Relaterede i den venstre navigationsrude, i sektionen **Administrer** , og klik derefter på **Mit domæne** for at åbne siden **Mit domæne** . 

    ![Mit domæne] (./media/active-directory-saas-jobscience-tutorial/IC767825.png "Mit domæne")

4.  Sørg for, at det er "**Trin 4 installeres til brugere**" for at bekræfte, at dit domæne er konfigureret korrekt, og gennemgå dine "**Indstillinger for mit domæne**".

    ![Doman brug af bruger] (./media/active-directory-saas-jobscience-tutorial/IC784377.png "Doman brug af bruger")

5.  Log på din Azure klassisk portal i et andet browservindue.

6.  På siden **Jobscience** application integration, klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** .

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-jobscience-tutorial/IC784360.png "Konfigurere Single Sign-On")

7.  Vælg **Microsoft Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge Jobscience** , og klik derefter på **Næste**.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-jobscience-tutorial/IC784361.png "Konfigurere Single Sign-On")

8.  Skriv din URL-adresse ved hjælp af følgende mønster "*http://company.my.salesforce.com*" på siden **Konfigurer App URL-adressen** i tekstfeltet **Jobscience log i URL-adresse** , og klik derefter på **Næste**.

    ![Konfigurere App URL-adresse] (./media/active-directory-saas-jobscience-tutorial/IC784362.png "Konfigurere App URL-adresse")

9.  Klik på **Hent certifikat**for at hente dit certifikat, på siden **Konfigurer single sign-on – på Jobscience** , og Gem derefter certifikatfilen lokalt på din computer.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-jobscience-tutorial/IC784363.png "Konfigurere Single Sign-On")

10. Klik på **Sikkerhedskontrol**på webstedet Jobscience virksomhed, og klik derefter på **Indstillinger for enkelt Sign-On**.

    ![Sikkerhedskontrol] (./media/active-directory-saas-jobscience-tutorial/IC784364.png "Sikkerhedskontrol")

11. I sektionen **Enkelt Sign-On Settings** skal du udføre følgende trin:

    ![Enkelt Sign-On-indstillinger] (./media/active-directory-saas-jobscience-tutorial/IC781026.png "Enkelt Sign-On-indstillinger")

    1.  Vælg **SAML aktiveret**.
    2.  Klik på **Ny**.

12. I dialogboksen **SAML enkelt Sign-On indstillingen Rediger** skal du udføre følgende trin:

    ![SAML enkelt Sign-On indstilling] (./media/active-directory-saas-jobscience-tutorial/IC784365.png "SAML enkelt Sign-On indstilling")

    1.  I tekstfeltet **navn** skal du skrive et navn til din konfiguration.
    2.  Kopiere værdien **Udsteder URL-adressen** på Azure-klassisk portalen på **Konfigurer single sign-on – på Jobscience** dialog siden, og Indsæt det i tekstfeltet **udsteder**
    3.  Skriv **https://salesforce-jobscience.com** i tekstfeltet **Enheds-Id**
    4.  Klik på **Gennemse** for at overføre dit Azure AD-certifikat.
    5.  Vælg **program indeholder sammenslutning-ID'ET fra brugerobjektet**som **SAML identitetstype**.
    6.  Vælg **identitet er i NameIdentfier elementet i sætningen emne**som **SAML identitet placering**.
    7.  Kopiere værdien **Remote Login URL-adressen** på Azure-klassisk portalen på **Konfigurer single sign-on – på Jobscience** dialog siden, og Indsæt det i tekstfeltet **Identitet udbyder Login URL-adresse**
    8.  Kopiere værdien **Remote log URL-adressen** på Azure-klassisk portalen på **Konfigurer single sign-on – på Jobscience** dialog siden, og Indsæt det i tekstfeltet **Identitet udbyder log URL-adresse**
    9.  Klik på **Gem**.

13. Klik på **Domain Management** for at udvide sektionen Relaterede i den venstre navigationsrude, i sektionen **Administrer** , og klik derefter på **Mit domæne** for at åbne siden **Mit domæne** . 

    ![Mit domæne] (./media/active-directory-saas-jobscience-tutorial/IC767825.png "Mit domæne")

14. Klik på **Rediger**på siden **Mit domæne** i sektionen **Login siden Branding** .

    ![Logonside Branding] (./media/active-directory-saas-jobscience-tutorial/IC767826.png "Logonside Branding")

15. På siden **Login siden Branding** i sektionen **Godkendelsestjenesten** vises navnet på din **SAML SSO indstillinger** . Markere den, og klik derefter på **Gem**.

    ![Logonside Branding] (./media/active-directory-saas-jobscience-tutorial/IC784366.png "Logonside Branding")

16. Vælge enkelt sign-on – konfiguration bekræftelsen på Azure klassisk-portalen, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** .

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-jobscience-tutorial/IC784367.png "Konfigurere Single Sign-On")
  
For at få SP Klik startet Single Sign-on Login URL-adressen på **Single Sign-On indstillinger** i sektionen **Sikkerhedskontrol** menu.

![Sikkerhedskontrol] (./media/active-directory-saas-jobscience-tutorial/IC784368.png "Sikkerhedskontrol")
  
Klik på den SSO-profil, du har oprettet i ovenstående trin.  
Denne side viser Single Sign på URL-adressen for din virksomhed (fx *https://companyname.my.salesforce.com?so=companyid*).
##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af
  
For at aktivere Azure AD-brugere at logge på Jobscience, skal de klargøres til Jobscience.  
Klargøring af er Jobscience, en manuel opgave.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>For at konfigurere brugerklargøring skal du udføre følgende trin:

1.  Log på webstedet **Jobscience** virksomhed som administrator.

2.  Gå til konfiguration af

    ![Konfiguration] (./media/active-directory-saas-jobscience-tutorial/IC784358.png "Konfiguration")

3.  Gå til **administrere brugere \> brugere**.

    ![Brugere] (./media/active-directory-saas-jobscience-tutorial/IC784369.png "Brugere")

4.  Klik på **ny bruger**.

    ![Alle brugere] (./media/active-directory-saas-jobscience-tutorial/IC784370.png "Alle brugere")

5.  I dialogboksen **Rediger bruger** skal du udføre følgende trin:

    ![Bruger redigere] (./media/active-directory-saas-jobscience-tutorial/IC784371.png "Bruger redigere")

    1.  Skriv fornavn, efternavn, alias, mail, bruger navn og kaldenavn egenskaberne for den Azure AD-bruger, du vil klargøre i den relaterede tekstbokse.
    2.  Klik på **Gem**.

    >[AZURE.NOTE] Azure AD-kontoindehaver får en mail, der indeholder et link for at bekræfte kontoen, før den er aktiveret.

>[AZURE.NOTE] Du kan bruge en hvilken som helst anden Jobscience bruger konto værktøjer til oprettelse eller API'er, som Jobscience til klargøring AAD brugerkonti.

##<a name="assigning-users"></a>Tildele brugere
  
Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-jobscience-perform-the-following-steps"></a>Hvis du vil tildele brugere til Jobscience, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.

2.  Klik på **Tildel brugere**på siden **Jobscience **application integration.

    ![Tildele brugere] (./media/active-directory-saas-jobscience-tutorial/IC784372.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Ja] (./media/active-directory-saas-jobscience-tutorial/IC767830.png "Ja")
  
Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang. Se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)kan finde flere oplysninger om panelet adgang.
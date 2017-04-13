<properties 
    pageTitle="Selvstudium: Azure Active Directory-integration med Work.com | Microsoft Azure" 
    description="Lær at bruge Work.com med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!." 
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
    ms.date="09/11/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-workcom"></a>Selvstudium: Azure Active Directory-integration med Work.com
  
Formålet med dette selvstudium er at vise integration af Azure og Work.com.  
Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   En Work.com single sign-on aktiveret abonnement
  
Når du har fuldført dette selvstudium, tildele de AAD brugere, som du har Work.com access bliver kunne enkelt log i programmet på webstedet Work.com virksomhed (service provider startet sign-on) eller ved hjælp af [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md).
  
Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integrationen for Work.com
2.  Konfigurere Enkeltlogon
3.  Konfiguration af brugerklargøring af
4.  Tildele brugere

![Scenarie] (./media/active-directory-saas-work-com-tutorial/IC794105.png "Scenarie")

##<a name="enabling-the-application-integration-for-workcom"></a>Aktivere programmet integrationen for Work.com
  
Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integrationen for Work.com.

###<a name="to-enable-the-application-integration-for-workcom-perform-the-following-steps"></a>For at aktivere programmet integration for Work.com skal du udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-work-com-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-work-com-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-work-com-tutorial/IC749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-work-com-tutorial/IC749322.png "Tilføj et program fra gallerry")

6.  I **søgefeltet**skal du skrive **Work.com**.

    ![Galleri med programmet] (./media/active-directory-saas-work-com-tutorial/IC794106.png "Galleri med programmet")

7.  Vælg **Work.com**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![Work.com] (./media/active-directory-saas-work-com-tutorial/IC794107.png "Work.com")

##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon
  
Formålet med dette afsnit er til at disponere hvordan du aktiverer brugere til at godkende Work.com med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen.  
Som en del af denne procedure, er det nødvendigt at overføre et certifikat til Work.com.com.

>[AZURE.NOTE] Hvis du vil konfigurere single sign-on, skal du konfigurere et brugerdefineret domænenavn til Work.com endnu. Du skal definere mindst et domænenavn, test dit domænenavn og installerer det til hele organisationen.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Log på din Work.com lejer som administrator.

2.  Gå til **konfiguration**.

    ![Konfiguration] (./media/active-directory-saas-work-com-tutorial/IC794108.png "Konfiguration")

3.  Klik på **Domain Management** for at udvide sektionen Relaterede i den venstre navigationsrude, i sektionen **Administrer** , og klik derefter på **Mit domæne** for at åbne siden **Mit domæne** . 

    ![Mit domæne] (./media/active-directory-saas-work-com-tutorial/IC767825.png "Mit domæne")

4.  Sørg for, at det er "**Trin 4 installeres til brugere**" for at bekræfte, at dit domæne er konfigureret korrekt, og gennemgå dine "**Indstillinger for mit domæne**".

    ![Doman brug af bruger] (./media/active-directory-saas-work-com-tutorial/IC784377.png "Doman brug af bruger")

5.  Log på din Azure klassisk portal i et andet browservindue.

6.  På siden **Work.com **application integration, klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** .

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-work-com-tutorial/IC794109.png "Konfigurere Single Sign-On")

7.  Vælg **Microsoft Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge Work.com** , og klik derefter på **Næste**.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-work-com-tutorial/IC794110.png "Konfigurere Single Sign-On")

8.  Skriv URL-adressen bruges af dine brugere til at logge din Work.com-program i tekstfeltet **Work.com log på URL-adressen** på siden **Konfigurer App URL-adresse** (f.eks.: " *http://company.my.salesforce.com*"), og klik derefter på **Næste**: 

    ![Konfigurere App URL-adresse] (./media/active-directory-saas-work-com-tutorial/IC794111.png "Konfigurere App URL-adresse")

9.  Klik på **Hent certifikat**for at hente dit certifikat, på siden **Konfigurer single sign-on – på Work.com** , og Gem derefter certifikatfilen lokalt på din computer.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-work-com-tutorial/IC794112.png "Konfigurere Single Sign-On")

10. Log på din Work.com lejer.

11. Gå til **konfiguration**.

    ![Konfiguration] (./media/active-directory-saas-work-com-tutorial/IC794108.png "Konfiguration")

12. Udvide menuen **Sikkerhedskontrol** , og klik derefter på **Indstillinger for enkelt Sign-On**.

    ![Enkelt Sign-On-indstillinger] (./media/active-directory-saas-work-com-tutorial/IC794113.png "Enkelt Sign-On-indstillinger")

13. På siden **Indstillinger for enkelt Sign-On** dialogboksen at udføre følgende trin:

    ![SAML aktiveret] (./media/active-directory-saas-work-com-tutorial/IC781026.png "SAML aktiveret")

    1.  Vælg **SAML aktiveret**.
    2.  Klik på **Ny**.

14. I sektionen **SAML enkelt Sign-On Settings** skal du udføre følgende trin:

    ![SAML enkelt Sign-On indstilling] (./media/active-directory-saas-work-com-tutorial/IC794114.png "SAML enkelt Sign-On indstilling")

    1.  I tekstfeltet **navn** skal du skrive et navn til din konfiguration.  

        >[AZURE.NOTE] Give en værdi for **navn** , udfyldes tekstboksen **API** automatisk.

    2.  Kopiér værdien **Udsteder URL-adressen** på Azure-klassisk portalen på **Konfigurer single sign-on – på Work.com** dialogboksen siden, og Indsæt det i tekstfeltet **udsteder** .
    3.  Klik på **Gennemse**for at overføre det certifikat, der er hentet.
    4.  Skriv **https://salesforce-work.com**i tekstfeltet **Enheds-Id** .
    5.  Vælg **program indeholder sammenslutning-ID'ET fra brugerobjektet**som **SAML identitetstype**.
    6.  Vælg **identitet er i NameIdentfier elementet i sætningen emne**som **SAML identitet placering**.
    7.  Kopiér værdien **Remote Login URL-adressen** på Azure-klassisk portalen på **Konfigurer single sign-on – på Work.com** dialogboksen siden, og Indsæt det i tekstfeltet **Identitet udbyder Login URL-adresse** .
    8.  Kopiér værdien **Remote log URL-adressen** på Azure-klassisk portalen på **Konfigurer single sign-on – på Work.com** dialogboksen siden, og Indsæt det i tekstfeltet **Identitet udbyder log URL-adresse** .
    9.  Vælg **HTTP Post**, som **Service Provider startet anmode om indbinding**.
    10. Klik på **Gem**.

15. Klik på **Domain Management** for at udvide sektionen Relaterede din Work.com klassisk-portalen på den venstre navigationsrude, og derefter klikke på **Mit domæne** for at åbne siden **Mit domæne** . 

    ![Mit domæne] (./media/active-directory-saas-work-com-tutorial/IC794115.png "Mit domæne")

16. Klik på **Rediger**på siden **Mit domæne** i sektionen **Login siden Branding** .

    ![Logonside Branding] (./media/active-directory-saas-work-com-tutorial/IC767826.png "Logonside Branding")

17. På siden **Login siden Branding** i sektionen **Godkendelsestjenesten** vises navnet på din **SAML SSO indstillinger** . Markere den, og klik derefter på **Gem**.

    ![Logonside Branding] (./media/active-directory-saas-work-com-tutorial/IC784366.png "Logonside Branding")

18. Vælge enkelt sign-on – konfiguration bekræftelsen på Azure klassisk-portalen, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** .

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-work-com-tutorial/IC794116.png "Konfigurere Single Sign-On")

##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af
  
Azure Active Directory-brugere skal kunne logge på, skal de klargøres til Work.com.  
Klargøring af er Work.com, en manuel opgave.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>For at konfigurere brugerklargøring skal du udføre følgende trin:

1.  Logge webstedet Work.com virksomhed som administrator.

2.  Gå til **konfiguration**.

    ![Konfiguration] (./media/active-directory-saas-work-com-tutorial/IC794108.png "Konfiguration")

3.  Gå til **administrere brugere \> brugere**.

    ![Administrere brugere] (./media/active-directory-saas-work-com-tutorial/IC784369.png "Administrere brugere")

4.  Klik på **ny bruger**.

    ![Alle brugere] (./media/active-directory-saas-work-com-tutorial/IC794117.png "Alle brugere")

5.  I sektionen bruger redigere skal du udføre følgende trin:

    ![Bruger redigere] (./media/active-directory-saas-work-com-tutorial/IC794118.png "Bruger redigere")

    1.  Skriv **Efternavn**, **Alias**, **mail**, **brugernavn** og **kaldenavn** attributterne for en gyldig Azure Active Directory-konto, du vil klargøre i den relaterede tekstbokse.
    2.  Vælg **rolle**, **brugerlicens** og **profil**.
    3.  Klik på **Gem**.  

        >[AZURE.NOTE] Azure Active Directory-kontoindehaver får mails, der indeholder et link for at bekræfte kontoen, før den bliver aktiv.

>[AZURE.NOTE] Du kan bruge en hvilken som helst anden Work.com bruger konto værktøjer til oprettelse eller API'er, som Work.com til klargøring AAD brugerkonti.

##<a name="assigning-users"></a>Tildele brugere
  
Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-workcom-perform-the-following-steps"></a>Hvis du vil tildele brugere til Work.com, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.

2.  Klik på **Tildel brugere**på siden Work.com application integration.

    ![Tildele brugere] (./media/active-directory-saas-work-com-tutorial/IC794119.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Ja] (./media/active-directory-saas-work-com-tutorial/IC767830.png "Ja")
  
Du bør nu vente 10 minutter og bekræfte, at kontoen er blevet synkroniseret med Work.com.com.
  
Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang. Se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)kan finde flere oplysninger om panelet adgang.
<properties 
    pageTitle="Selvstudium: Azure Active Directory-integration med IdeaScale | Microsoft Azure" 
    description="Lær, hvordan du bruger IdeaScale med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!" 
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

#<a name="tutorial-azure-active-directory-integration-with-ideascale"></a>Selvstudium: Azure Active Directory-integration med IdeaScale
  
Formålet med dette selvstudium er at vise integration af Azure og IdeaScale.  
Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   En IdeaScale single sign-on aktiveret abonnement
  
Når du har fuldført dette selvstudium, vil de Azure AD-brugere, du har tildelt til IdeaScale kunne enkelt Log på det program, ved hjælp af [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md).
  
Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integrationen for IdeaScale
2.  Konfigurere Enkeltlogon
3.  Konfiguration af brugerklargøring af
4.  Tildele brugere

![Scenarie] (./media/active-directory-saas-ideascale-tutorial/IC790838.png "Scenarie")
##<a name="enabling-the-application-integration-for-ideascale"></a>Aktivere programmet integrationen for IdeaScale
  
Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integrationen for IdeaScale.

###<a name="to-enable-the-application-integration-for-ideascale-perform-the-following-steps"></a>For at aktivere programmet integration for IdeaScale skal du udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-ideascale-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-ideascale-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-ideascale-tutorial/IC749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-ideascale-tutorial/IC749322.png "Tilføj et program fra gallerry")

6.  I **søgefeltet**skal du skrive **IdeaScale**.

    ![Galleri med programmet] (./media/active-directory-saas-ideascale-tutorial/IC790841.png "Galleri med programmet")

7.  Vælg **IdeaScale**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![IdeaScale] (./media/active-directory-saas-ideascale-tutorial/IC790842.png "IdeaScale")
##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon
  
Formålet med dette afsnit er til at disponere hvordan du aktiverer brugere til at godkende IdeaScale med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen.  
Konfiguration af single sign-on til IdeaScale, skal du hente en miniatureværdi fra et certifikat.  
Hvis du ikke kender med denne procedure, se, [hvordan du hente et certifikat miniature værdi](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **IdeaScale** application integration.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-ideascale-tutorial/IC790843.png "Konfigurere Single Sign-On")

2.  Vælg **Microsoft Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge IdeaScale** , og klik derefter på **Næste**.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-ideascale-tutorial/IC790844.png "Konfigurere Single Sign-On")

3.  Skriv URL-adressen bruges af dine brugere til at logge din IdeaScale-program i tekstfeltet **IdeaScale Log på URL-adressen** på siden **Konfigurer App URL-adresse** (f.eks.: "*https://company.IdeaScale.com*"), og klik derefter på **Næste**.

    ![Konfigurere App URL-adresse] (./media/active-directory-saas-ideascale-tutorial/IC790845.png "Konfigurere App URL-adresse")

4.  Klik på **Hent metadata**for at hente metadata for din, på siden **Konfigurer single sign-on – på IdeaScale** , og Gem derefter metadatafilen lokalt på din computer.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-ideascale-tutorial/IC790846.png "Konfigurere Single Sign-On")

5.  Log på webstedet IdeaScale virksomhed som administrator i et andet browservindue.

6.  Gå til **Indstillinger for Community**.

    ![Indstillinger for community] (./media/active-directory-saas-ideascale-tutorial/IC790847.png "Indstillinger for community")

7.  Gå til **sikkerhed \> enkelt in indstillinger**.

    ![Enkelt in indstillinger] (./media/active-directory-saas-ideascale-tutorial/IC790848.png "Enkelt in indstillinger")

8.  Vælg **SAML 2.0**som **Enkelt in Type**.

    ![Enkelt in Type] (./media/active-directory-saas-ideascale-tutorial/IC790849.png "Enkelt in Type")

9.  I dialogboksen **Indstillinger for enkelt in** skal du udføre følgende trin:

    ![Enkelt in indstillinger] (./media/active-directory-saas-ideascale-tutorial/IC790850.png "Enkelt in indstillinger")

    1.  Kopiér værdien **Enheds-ID** i Azure klassisk-portalen på **Konfigurer single sign-on – på IdeaScale** dialogboksen siden, og Indsæt det i tekstfeltet **SAML IdP enheds-ID** .
    2.  Kopiere indholdet af metadatafilen hentede, og Indsæt det i tekstfeltet **SAML IdP Metadata** .
    3.  Kopiér værdien **Remote log URL-adressen** på Azure-klassisk portalen på **Konfigurer single sign-on – på IdeaScale** dialogboksen siden, og Indsæt det i tekstfeltet **Log succes URL-adresse** .
    4.  Klik på **Gem ændringer**.

10. Vælge enkelt sign-on – konfiguration bekræftelsen på Azure klassisk-portalen, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** .

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-ideascale-tutorial/IC790851.png "Konfigurere Single Sign-On")
##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af
  
For at aktivere Azure AD-brugere at logge på IdeaScale, skal de klargøres til IdeaScale.  
Klargøring af er IdeaScale, en manuel opgave.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>For at konfigurere brugerklargøring skal du udføre følgende trin:

1.  Log på webstedet **IdeaScale** virksomhed som administrator.

2.  Gå til **Indstillinger for Community**.

    ![Indstillinger for community] (./media/active-directory-saas-ideascale-tutorial/IC790847.png "Indstillinger for community")

3.  Gå til **grundlæggende indstillinger for \> Medlemsstyring**.

4.  Klik på **Tilføj medlem**.

    ![Medlemsstyring] (./media/active-directory-saas-ideascale-tutorial/IC790852.png "Medlemsstyring")

5.  I sektionen Tilføj nyt medlem skal du udføre følgende trin:

    ![Tilføje nyt medlem] (./media/active-directory-saas-ideascale-tutorial/IC790853.png "Tilføje nyt medlem")

    1.  Skriv en gyldig AAD-konto, du vil klargøre mailadresse i tekstfeltet **Mailadresser** .
    2.  Klik på **Gem ændringer**.

    >[AZURE.NOTE] Azure Active Directory-kontoindehaver får en mail med et link for at bekræfte kontoen, før den bliver aktiv.

>[AZURE.NOTE] Du kan bruge en hvilken som helst anden IdeaScale bruger konto værktøjer til oprettelse eller API'er, som IdeaScale til klargøring AAD brugerkonti.

##<a name="assigning-users"></a>Tildele brugere
  
Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-ideascale-perform-the-following-steps"></a>Hvis du vil tildele brugere til IdeaScale, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.

2.  Klik på **Tildel brugere**på siden **IdeaScale **application integration.

    ![Tildele brugere] (./media/active-directory-saas-ideascale-tutorial/IC790854.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Ja] (./media/active-directory-saas-ideascale-tutorial/IC767830.png "Ja")
  
Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang. Se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)kan finde flere oplysninger om panelet adgang.
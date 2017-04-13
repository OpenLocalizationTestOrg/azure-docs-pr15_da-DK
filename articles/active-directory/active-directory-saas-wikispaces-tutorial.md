<properties 
    pageTitle="Selvstudium: Azure Active Directory-integration med Wikispaces | Microsoft Azure" 
    description="Lær at bruge Wikispaces med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!." 
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

#<a name="tutorial-azure-active-directory-integration-with-wikispaces"></a>Selvstudium: Azure Active Directory-integration med Wikispaces
  
Formålet med dette selvstudium er at vise integration af Azure og Wikispaces.  
Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   En Wikispaces single sign-on aktiveret abonnement
  
Når du har fuldført dette selvstudium, vil de Azure AD-brugere, du har tildelt til Wikispaces kunne enkelt Log på programmet på webstedet Wikispaces virksomhed (service provider startet sign-on) eller ved hjælp af [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md).
  
Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integrationen for Wikispaces
2.  Konfigurere Enkeltlogon
3.  Konfiguration af brugerklargøring af
4.  Tildele brugere

![Sceanrio] (./media/active-directory-saas-wikispaces-tutorial/IC787182.png "Sceanrio")

##<a name="enabling-the-application-integration-for-wikispaces"></a>Aktivere programmet integrationen for Wikispaces
  
Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integrationen for Wikispaces.

###<a name="to-enable-the-application-integration-for-wikispaces-perform-the-following-steps"></a>For at aktivere programmet integration for Wikispaces skal du udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-wikispaces-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-wikispaces-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-wikispaces-tutorial/IC749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-wikispaces-tutorial/IC749322.png "Tilføj et program fra gallerry")

6.  I **søgefeltet**skal du skrive **Wikispaces**.

    ![Galleri med programmet] (./media/active-directory-saas-wikispaces-tutorial/IC787186.png "Galleri med programmet")

7.  Vælg **Wikispaces**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![Wikispaces] (./media/active-directory-saas-wikispaces-tutorial/IC787187.png "Wikispaces")

##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon
  
Formålet med dette afsnit er til at disponere hvordan du aktiverer brugere til at godkende Wikispaces med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **Wikispaces** application integration.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-wikispaces-tutorial/IC787188.png "Konfigurere Single Sign-On")

2.  Vælg **Microsoft Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge Wikispaces** , og klik derefter på **Næste**.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-wikispaces-tutorial/IC787189.png "Konfigurere Single Sign-On")

3.  Skriv din URL-adresse ved hjælp af følgende mønster "*http://company.wikispaces.net*" på siden **Konfigurer App URL-adressen** i tekstfeltet **Wikispaces Log på URL-adressen** , og klik derefter på **Næste**.

    ![Konfigurere App URL-adresse] (./media/active-directory-saas-wikispaces-tutorial/IC787190.png "Konfigurere App URL-adresse")

4.  Klik på **Hent metadata**på siden **Konfigurer single sign-on – på Wikispaces** , og Gem derefter Metadatafilen på din computer.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-wikispaces-tutorial/IC787191.png "Konfigurere Single Sign-On")

5.  Sende metadatafile til Wikispaces-supportteam.

    >[AZURE.NOTE] Konfigurationen af enkelt sign-on – der skal udføres af Wikispaces supportteam. Du får en meddelelse, så snart konfigurationen er fuldført.

6.  Vælge enkelt sign-on – konfiguration bekræftelsen på Azure klassisk-portalen, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** .

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-wikispaces-tutorial/IC787192.png "Konfigurere Single Sign-On")

##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af
  
For at aktivere Azure AD-brugere at logge på Wikispaces, skal de klargøres til Wikispaces.  
Klargøring af er Wikispaces, en manuel opgave.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Hvis du vil klargøre en brugerkonti, skal du udføre følgende trin:

1.  Log på webstedet **Wikispaces** virksomhed som administrator.

2.  Gå til **medlemmer**.

    ![Medlemmer] (./media/active-directory-saas-wikispaces-tutorial/IC787193.png "Medlemmer")

3.  Klik på **Inviter personer**.

    ![Invitere personer] (./media/active-directory-saas-wikispaces-tutorial/IC787194.png "Invitere personer")

4.  I sektionen **Inviter personer** skal du udføre følgende trin:

    ![Invitere personer] (./media/active-directory-saas-wikispaces-tutorial/IC787208.png "Invitere personer")

    1.  Skriv **brugernavne eller e-mail-adresse** for en gyldig AAD-konto, du vil klargøre i den relaterede tekstbokse.
    2.  Klik på **Send**.  

        >[AZURE.NOTE] Azure Active Directory-kontoindehaver modtager mails, der indeholder et link for at bekræfte kontoen, før den bliver aktiv.

>[AZURE.NOTE] Du kan bruge en hvilken som helst anden Wikispaces bruger konto værktøjer til oprettelse eller API'er, som Wikispaces til klargøring AAD brugerkonti.

##<a name="assigning-users"></a>Tildele brugere
  
Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-wikispaces-perform-the-following-steps"></a>Hvis du vil tildele brugere til Wikispaces, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.

2.  Klik på **Tildel brugere**på siden **Wikispaces **application integration.

    ![Tildele brugere] (./media/active-directory-saas-wikispaces-tutorial/IC787195.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Ja] (./media/active-directory-saas-wikispaces-tutorial/IC767830.png "Ja")
  
Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang. Se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)kan finde flere oplysninger om panelet adgang.
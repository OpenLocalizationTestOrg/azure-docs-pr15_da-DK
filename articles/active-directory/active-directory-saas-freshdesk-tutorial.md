<properties 
    pageTitle="Selvstudium: Azure Active Directory-Integration med Freshdesk | Microsoft Azure" 
    description="Lær, hvordan du bruger Freshdesk med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!" 
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

#<a name="tutorial-azure-active-directory-integration-with-freshdesk"></a>Selvstudium: Azure Active Directory-Integration med Freshdesk
  
Formålet med dette selvstudium er at vise integration af Azure og Freshdesk.  
Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   En Freshdesk lejer
  
Når du har fuldført dette selvstudium, vil de Azure AD-brugere, du har tildelt til Freshdesk kunne enkelt Log på programmet på webstedet Freshdesk virksomhed (service provider startet sign-on) eller ved hjælp af [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md).
  
Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integrationen for Freshdesk
2.  Konfigurere Enkeltlogon
3.  Konfiguration af brugerklargøring af
4.  Tildele brugere

![Scenarie] (./media/active-directory-saas-freshdesk-tutorial/IC776761.png "Scenarie")
##<a name="enabling-the-application-integration-for-freshdesk"></a>Aktivere programmet integrationen for Freshdesk
  
Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integrationen for Freshdesk.

###<a name="to-enable-the-application-integration-for-freshdesk-perform-the-following-steps"></a>For at aktivere programmet integration for Freshdesk skal du udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-freshdesk-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-freshdesk-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-freshdesk-tutorial/IC749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-freshdesk-tutorial/IC749322.png "Tilføj et program fra gallerry")

6.  I **søgefeltet**skal du skrive **Freshdesk**.

    ![Galleri med programmet] (./media/active-directory-saas-freshdesk-tutorial/IC776762.png "Galleri med programmet")

7.  Vælg **Freshdesk**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![Freshdesk] (./media/active-directory-saas-freshdesk-tutorial/IC776763.png "Freshdesk")
##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon
  
Formålet med dette afsnit er til at disponere hvordan du aktiverer brugere til at godkende Freshdesk med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen.  
Konfiguration af single sign-on til Freshdesk, skal du hente en miniatureværdi fra et certifikat.  
Hvis du ikke kender med denne procedure, se, [hvordan du hente et certifikat miniature værdi](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **Freshdesk** application integration.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-freshdesk-tutorial/IC776764.png "Konfigurere Single Sign-On")

2.  Vælg **Microsoft Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge Freshdesk** , og klik derefter på **Næste**.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-freshdesk-tutorial/IC776765.png "Konfigurere Single Sign-On")

3.  Skriv din URL-adresse ved hjælp af følgende mønster i tekstfeltet **Freshdesk log i URL-adresse** på siden **Konfigurer App URL-adresse** "*https://\<lejer navn\>. Freshdesk.com*", og klik derefter på **Næste**.

    ![Konfigurere App URL-adresse] (./media/active-directory-saas-freshdesk-tutorial/IC776766.png "Konfigurere App URL-adresse")

4.  På siden **Konfigurer single sign-on – på Freshdesk** for at downloade certifikatet, skal du klikke på **Hent certifikat**, og Gem derefter certifikatfil lokalt som **c:\\Freshdesk.cer**.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-freshdesk-tutorial/IC776767.png "Konfigurere Single Sign-On")

5.  Log på webstedet Freshdesk virksomhed som administrator i et andet browservindue.

6.  Klik på **administrator**i menuen øverst.

    ![Administrator] (./media/active-directory-saas-freshdesk-tutorial/IC776768.png "Administrator")

7.  Klik på **sikkerhed**under fanen **Generelle indstillinger** .

    ![Sikkerhed] (./media/active-directory-saas-freshdesk-tutorial/IC776769.png "Sikkerhed")

8.  I sektionen **sikkerhed** skal du udføre følgende trin:

    ![Single Sign-On] (./media/active-directory-saas-freshdesk-tutorial/IC776770.png "Single Sign-On")

    1.  Vælg **på**for **Enkelt Log på (SSO)**.
    2.  Vælg **SAML SSO**.
    3.  Kopiér værdien **Remote Login URL-adressen** på Azure-klassisk portalen på **Konfigurer single sign-on – på Freshdesk** dialogboksen siden, og Indsæt det i tekstfeltet **SAML Login URL-adresse** .
    4.  Kopiér værdien **Remote log URL-adressen** på Azure-klassisk portalen på **Konfigurer single sign-on – på Freshdesk** dialogboksen siden, og Indsæt det i tekstfeltet **Log URL-adresse** .
    5.  Kopiér værdien **miniature** fra det eksporterede certifikat, og Indsæt det i tekstfeltet **Sikkerhed certifikat fingeraftryk** .  

        >[AZURE.TIP]Du kan finde flere oplysninger, se, [hvordan du hente et certifikat miniature værdi](http://youtu.be/YKQF266SAxI)

    6.  Klik på **Gem**.

9.  Vælge enkelt sign-on – konfiguration bekræftelsen på Azure klassisk-portalen, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** .

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-freshdesk-tutorial/IC776771.png "Konfigurere Single Sign-On")
##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af
  
For at aktivere Azure AD-brugere at logge på Freshdesk, skal de klargøres til Freshdesk.  
Klargøring af er Freshdesk, en manuel opgave.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Hvis du vil klargøre en brugerkonti, skal du udføre følgende trin:

1.  Log på din **Freshdesk** lejer.

2.  Klik på **administrator**i menuen øverst.

    ![Administrator] (./media/active-directory-saas-freshdesk-tutorial/IC776772.png "Administrator")

3.  Klik på **supportmedarbejdere**i fanen **Generelle indstillinger** .

    ![Supportmedarbejdere] (./media/active-directory-saas-freshdesk-tutorial/IC776773.png "Supportmedarbejdere")

4.  Klik på **Ny Agent**.

    ![Ny Agent] (./media/active-directory-saas-freshdesk-tutorial/IC776774.png "Ny Agent")

5.  I dialogboksen oplysninger om brugere skal du udføre følgende trin:

    ![Agentoplysninger] (./media/active-directory-saas-freshdesk-tutorial/IC776775.png "Agentoplysninger")

    1.  Skriv navnet på den Azure AD-konto, du vil klargøre i tekstfeltet **Fulde navn** .
    2.  Skriv den Azure AD-konto, du vil klargøre Azure AD mailadresse i tekstfeltet **mail** .
    3.  Skriv titlen på den Azure AD-konto, du vil klargøre i tekstfeltet **Titel** .
    4.  Vælg **supportmedarbejdere rolle**, og klik derefter på **Tildel**.
    5.  Klik på **Gem**.
    
        >[AZURE.NOTE] Azure AD-kontoindehaver får en mail, der indeholder et link for at bekræfte kontoen, før den er aktiveret.

>[AZURE.NOTE] Du kan bruge en hvilken som helst anden Freshdesk bruger konto værktøjer til oprettelse eller API'er, som Freshdesk til klargøring AAD brugerkonti.

##<a name="assigning-users"></a>Tildele brugere
  
Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-freshdesk-perform-the-following-steps"></a>Hvis du vil tildele brugere til Freshdesk, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.

2.  Klik på **Tildel brugere**på siden **Freshdesk **application integration.

    ![Tildele brugere] (./media/active-directory-saas-freshdesk-tutorial/IC776776.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Ja] (./media/active-directory-saas-freshdesk-tutorial/IC767830.png "Ja")
  
Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang. Se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)kan finde flere oplysninger om panelet adgang.
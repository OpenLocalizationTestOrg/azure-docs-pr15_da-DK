<properties 
    pageTitle="Selvstudium: Azure Active Directory-integration med Zoho Mail | Microsoft Azure" 
    description="Lær at bruge Zoho Mail med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!." 
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
    ms.date="09/09/2016" 
    ms.author="markvi" />

#<a name="tutorial-azure-active-directory-integration-with-zoho-mail"></a>Selvstudium: Azure Active Directory-integration med Zoho Mail
  
Formålet med dette selvstudium er at vise integration af Azure og Zoho Mail.  
Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   En Zoho Mail-lejer
  
Når du har fuldført dette selvstudium, vil de Azure AD-brugere, du har tildelt til Zoho Mail ikke kunne enkelt Log på programmet på webstedet Zoho Mail virksomhed (service provider startet sign-on) eller ved hjælp af [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md).
  
Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integrationen for Zoho Mail
2.  Konfigurere Enkeltlogon
3.  Konfiguration af brugerklargøring af
4.  Tildele brugere

![Scenarie] (./media/active-directory-saas-zoho-mail-tutorial/IC789600.png "Scenarie")

##<a name="enabling-the-application-integration-for-zoho-mail"></a>Aktivere programmet integrationen for Zoho Mail
  
Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integrationen for Zoho Mail.

###<a name="to-enable-the-application-integration-for-zoho-mail-perform-the-following-steps"></a>For at aktivere programmet integration for Zoho Mail skal du udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-zoho-mail-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-zoho-mail-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-zoho-mail-tutorial/IC749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-zoho-mail-tutorial/IC749322.png "Tilføj et program fra gallerry")

6.  I **søgefeltet**skal du skrive **Zoho Mail**.

    ![Galleri med programmet] (./media/active-directory-saas-zoho-mail-tutorial/IC789601.png "Galleri med programmet")

7.  Vælg **Zoho Mail**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![Zoho Mail] (./media/active-directory-saas-zoho-mail-tutorial/IC789602.png "Zoho Mail")

##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon
  
Formålet med dette afsnit er til at disponere hvordan du aktiverer brugere til at godkende Zoho Mail med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen.  
Du er påkrævet for at oprette en base-64 certifikatfil for kodet som en del af denne procedure.  
Hvis du ikke kender med denne procedure, se, [hvordan du konverterer et binært certifikat til en tekstfil](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **Zoho Mail** application integration.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-zoho-mail-tutorial/IC789603.png "Konfigurere Single Sign-On")

2.  Vælg **Microsoft Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge Zoho Mail** , og klik derefter på **Næste**.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-zoho-mail-tutorial/IC789604.png "Konfigurere Single Sign-On")

3.  På siden **Konfigurer App URL-adresse** skal du udføre følgende trin:

    ![Konfigurere App URL-adresse] (./media/active-directory-saas-zoho-mail-tutorial/IC789605.png "Konfigurere App URL-adresse")

    en. I tekstfeltet **Zoho Mail Log på URL-adresse** skal du skrive din URL-adresse ved hjælp af følgende mønster:`http://<company name>.ZohoMail.com`

    b. Klik på **Næste**.


4.  Klik på **Hent certifikat**på siden **Konfigurer single sign-on – på Zoho Mail** , og Gem derefter certifikatfilen på din computer.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-zoho-mail-tutorial/IC789606.png "Konfigurere Single Sign-On")

5.  Log på din Zoho Mail virksomheds websted som en administrator i et andet browservindue.

6.  Gå til **Kontrolpanel**.

    ![I Kontrolpanel] (./media/active-directory-saas-zoho-mail-tutorial/IC789607.png "I Kontrolpanel")

7.  Klik på fanen **SAML godkendelse** .

    ![SAML godkendelse] (./media/active-directory-saas-zoho-mail-tutorial/IC789608.png "SAML godkendelse")

8.  I afsnittet **Detaljer om godkendelse af SAML** du udføre følgende trin:

    ![SAML godkendelse detaljer] (./media/active-directory-saas-zoho-mail-tutorial/IC789609.png "SAML godkendelse detaljer")

    1.  Kopiér værdien **Remote Login URL-adressen** på Azure-klassisk portalen på dialogboksside **konfigurere Enkeltlogon på Zoho Mail** , og Indsæt det i tekstfeltet **URL-adressen til logon** .
    2.  Kopiér værdien **Remote log URL-adressen** på Azure-klassisk portalen på dialogboksside **konfigurere Enkeltlogon på Zoho Mail** , og Indsæt det i tekstfeltet **Log URL-adresse** .
    3.  Kopiér værdien **Skift adgangskode URL-adressen** på Azure-klassisk portalen på dialogboksside **konfigurere Enkeltlogon på Zoho Mail** , og Indsæt det i tekstfeltet **URL-adressen Skift adgangskode** .
    4.  Oprette en **base-64-kodet** fil fra dit hentede certifikat.  

        >[AZURE.TIP] Du kan finde flere oplysninger, se, [hvordan du konverterer et binært certifikat til en tekstfil](http://youtu.be/PlgrzUZ-Y1o)

    5.  Åbn dit base-64-kodet certifikat i Notesblok, kopiere indholdet af den i Udklipsholder og sætte den ind på tekstfeltet **PublicKey** .
    6.  Vælg **RSA**som **algoritme**.
    7.  Klik på **OK**.

9.  Vælge enkelt sign-on – konfiguration bekræftelsen på Azure klassisk-portalen, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** .

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-zoho-mail-tutorial/IC789610.png "Konfigurere Single Sign-On")

##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af
  
For at aktivere Azure AD-brugere til at logge ind i en Zoho Mail, skal de klargøres til Zoho Mail.  
Klargøring af er Zoho Mail, en manuel opgave.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Hvis du vil klargøre en brugerkonti, skal du udføre følgende trin:

1.  Log på webstedet **Zoho Mail** virksomhed som administrator.

2.  Gå til **Kontrolpanel \> Mail og dokumenter**.

3.  Gå til **Brugeroplysninger \> Tilføj bruger**.

    ![Tilføj bruger] (./media/active-directory-saas-zoho-mail-tutorial/IC789611.png "Tilføj bruger")

4.  I dialogboksen **Tilføj brugere** skal du udføre følgende trin:

    ![Tilføj bruger] (./media/active-directory-saas-zoho-mail-tutorial/IC789612.png "Tilføj bruger")

    1.  Skriv **Fornavn**, **Efternavn**, **E-mail-ID**, **adgangskode** til en gyldig Azure Active Directory-konto, du vil klargøre i den relaterede tekstbokse.
    2.  Klik på **OK**.  

        >[AZURE.NOTE] Azure Active Directory-kontoindehaver, modtager en mail med et link for at bekræfte kontoen, før den bliver aktiv.

>[AZURE.NOTE] Du kan bruge en hvilken som helst anden Zoho Mail bruger konto værktøjer til oprettelse eller API'er, som Zoho Mail til klargøring AAD brugerkonti.

##<a name="assigning-users"></a>Tildele brugere
  
Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-zoho-mail-perform-the-following-steps"></a>Hvis du vil tildele brugere til Zoho Mail, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.

2.  Klik på **Tildel brugere**på siden **Zoho Mail **application integration.

    ![Tildele brugere] (./media/active-directory-saas-zoho-mail-tutorial/IC789613.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Ja] (./media/active-directory-saas-zoho-mail-tutorial/IC767830.png "Ja")
  
Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang. Se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)kan finde flere oplysninger om panelet adgang.
<properties 
    pageTitle="Selvstudium: Azure Active Directory-integration med Central skrivebord | Microsoft Azure" 
    description="Lær, hvordan du bruger Central skrivebord med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!" 
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

#<a name="tutorial-azure-active-directory-integration-with-central-desktop"></a>Selvstudium: Azure Active Directory-integration med Central skrivebord

Formålet med dette selvstudium er at vise integration af Azure og Central computeren. Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   En Central computeren single sign-on aktiveret abonnement / Central skrivebord lejer

Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integrationen Central computeren
2.  Konfigurere Enkeltlogon
3.  Konfiguration af brugerklargøring af
4.  Tildele brugere

![Scenarie] (./media/active-directory-saas-central-desktop-tutorial/IC769558.png "Scenarie")
##<a name="enabling-the-application-integration-for-central-desktop"></a>Aktivere programmet integrationen Central computeren

Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integrationen Central computeren.

###<a name="to-enable-the-application-integration-for-central-desktop-perform-the-following-steps"></a>For at aktivere programmet integrationen Central computeren skal du udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-central-desktop-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-central-desktop-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-central-desktop-tutorial/IC749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-central-desktop-tutorial/IC749322.png "Tilføj et program fra gallerry")

6.  I **søgefeltet**skal du skrive **Central skrivebord**.

    ![Galleri med programmet] (./media/active-directory-saas-central-desktop-tutorial/IC769559.png "Galleri med programmet")

7.  Vælge **Central skrivebord**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![Central skrivebord] (./media/active-directory-saas-central-desktop-tutorial/IC769560.png "Central skrivebord")
##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon

Formålet med dette afsnit er til at disponere hvordan du aktiverer brugere til at godkende Central skrivebord med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen.  
Du er påkrævet for at overføre et base-64-kodet certifikat til din Central skrivebord lejer som en del af denne procedure.  
Hvis du ikke kender med denne procedure, se, [hvordan du konverterer et binært certifikat til en tekstfil](http://youtu.be/PlgrzUZ-Y1o).



###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **Central skrivebord** application integration.

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-central-desktop-tutorial/IC749323.png "Konfigurere Enkeltlogon")

2.  Vælg **Microsoft Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge Central skrivebord** , og klik derefter på **Næste**.

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-central-desktop-tutorial/IC777628.png "Konfigurere Enkeltlogon")

3.  Udfør følgende trin på siden **Konfigurer App URL-adressen** , og klik derefter på **Næste**: 

    -   Skriv URL-adressen for din Central skrivebord lejer i tekstfeltet **Central skrivebord log i URL-adresse** (f.eks.: *http://contoso.centraldesktop.com*).
    -   Skriv dit skrivebord AssertionConsumerService URL-adressen Central i tekstfeltet Central skrivebord svar URL-adresse (f.eks.: https://contoso.centraldesktop.com/saml2-assertion.php).

    >[AZURE.NOTE] Du kan få vist værdien fra central computeren metadataene (f.eks.: *http://contoso.centraldesktop.com*).

    ![Konfigurere app URL-adresse] (./media/active-directory-saas-central-desktop-tutorial/IC769561.png "Konfigurere app URL-adresse")

4.  Klik på **Hent certifikat**for at hente dit certifikat, på siden **Konfigurer single sign-on – på Central skrivebord** , og derefter gemme certifikatfilen på din computer.

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-central-desktop-tutorial/IC769562.png "Konfigurere Enkeltlogon")

5.  Log på din **Central skrivebord** lejer.

6.  Gå til **Indstillinger**, klik på **Avanceret**, og klik derefter på **Single Sign-On**.

    ![Konfigurere – Avanceret] (./media/active-directory-saas-central-desktop-tutorial/IC769563.png "Konfigurere – Avanceret")

7.  På enkelt Log på siden **Indstillinger for** at udføre følgende trin:

    ![Single Sign-On indstillinger] (./media/active-directory-saas-central-desktop-tutorial/IC769564.png "Single Sign-On indstillinger")

    1.  Vælg **Aktivér SAML v2 Single Sign-On**.
    2.  Kopiér værdien **Udsteder URL-adressen** på Azure-klassisk portalen på siden **Konfigurer single sign-on – på Central skrivebord** , og Indsæt det i tekstfeltet **SSO URL-adresse** .
    3.  Kopiér værdien **Remote Login URL-adressen** på Azure-klassisk portalen på siden **Konfigurer single sign-on – på Central skrivebord** , og Indsæt det i tekstfeltet **SSO Login URL-adresse** .
    4.  Kopiér værdien **Enkelt Sign-Out URL-adressen** på Azure-klassisk portalen på siden **Konfigurer single sign-on – på Central skrivebord** , og Indsæt det i tekstfeltet **SSO log URL-adresse** .

8.  I sektionen **Meddelelse metode til bekræftelse af signatur** skal du udføre følgende trin:

    ![Meddelelse metode til bekræftelse af signatur] (./media/active-directory-saas-central-desktop-tutorial/IC769565.png "Meddelelse metode til bekræftelse af signatur")

    1.  Vælg **certifikat**.
    2.  Vælg **RSH SHA256**fra listen **SSO certifikat** .
    3.  Oprette en tekstfil fra hentede certifikatet, kopiere indholdet af tekstfilen, og Indsæt det i feltet **SSO certifikat** .  

        >[AZURE.TIP] Du kan finde flere oplysninger, se, [hvordan du konverterer et binært certifikat til en tekstfil](http://youtu.be/PlgrzUZ-Y1o)

    4.  Markér **Vis et link til siden SAMLv2 logon**.

9.  Klik på **Opdater**.

10. Vælge enkelt sign-on – konfiguration bekræftelsen på Azure klassisk-portalen, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** .

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-central-desktop-tutorial/IC769566.png "Konfigurere Enkeltlogon")
##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af

For AAD brugere skal kunne logge på, skal de klargøres til programmet Central skrivebord. Dette afsnit beskriver, hvordan du opretter AAD brugerkonti i Central på computeren.

###<a name="to-provision-user-accounts-to-central-desktop"></a>Til at klargøre brugerkonti til Central computer:

1.  Log på din Central skrivebord lejer.

2.  Gå til **personer \> interne medlemmer**.

3.  Klik på **Tilføj interne medlemmer**.

    ![Personer] (./media/active-directory-saas-central-desktop-tutorial/IC781051.png "Personer")

4.  I tekstfeltet **E-mail-adresse for nye medlemmer** skal du skrive en AAD-konto, du vil klargøre, og klik derefter på **Næste**.

    ![Mailadresserne på de nye medlemmer] (./media/active-directory-saas-central-desktop-tutorial/IC781052.png "Mailadresserne på de nye medlemmer")

5.  Klik på **Tilføj interne medlemmer**.

    ![Tilføje interne medlem] (./media/active-directory-saas-central-desktop-tutorial/IC781053.png "Tilføje interne medlem")

    >[AZURE.NOTE] De brugere, du har tilføjet, modtager en mail, der indeholder bekræftelse hyperlink til de skal bruge Klik her for at aktivere kontoen.

>[AZURE.NOTE] Du kan bruge en hvilken som helst anden Central skrivebord bruger konto værktøjer til oprettelse eller API'er, som Central skrivebord til klargøring AAD brugerkonti

##<a name="assigning-users"></a>Tildele brugere

Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-central-desktop-perform-the-following-steps"></a>Hvis du vil tildele brugere til Central computer, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.

2.  Klik på **Tildel brugere**på siden **Central skrivebord** application integration.

    ![Tildele brugere] (./media/active-directory-saas-central-desktop-tutorial/IC769567.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Ja] (./media/active-directory-saas-central-desktop-tutorial/IC767830.png "Ja")

Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang. Se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)kan finde flere oplysninger om panelet adgang.

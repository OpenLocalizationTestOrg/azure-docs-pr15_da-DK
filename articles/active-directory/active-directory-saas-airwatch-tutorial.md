<properties 
    pageTitle="Selvstudium: Azure Active Directory-integration med AirWatch | Microsoft Azure" 
    description="Lær, hvordan du bruger AirWatch med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!" 
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

#<a name="tutorial-azure-active-directory-integration-with-airwatch"></a>Selvstudium: Azure Active Directory-integration med AirWatch

Formålet med dette selvstudium er at vise integration af Azure og AirWatch.  
Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   En AirWatch single sign-on aktiveret abonnement

Når du har fuldført dette selvstudium, vil de Azure AD-brugere, du har tildelt til AirWatch kunne enkelt Log på programmet på webstedet AirWatch virksomhed (service provider startet sign-on) eller ved hjælp af [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md).

Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integrationen for AirWatch
2.  Konfigurere Enkeltlogon
3.  Konfiguration af brugerklargøring af
4.  Tildele brugere

![AirWatch] (./media/active-directory-saas-airwatch-tutorial/IC791913.png "AirWatch")
##<a name="enabling-the-application-integration-for-airwatch"></a>Aktivere programmet integrationen for AirWatch

Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integrationen for AirWatch.

###<a name="to-enable-the-application-integration-for-airwatch-perform-the-following-steps"></a>For at aktivere programmet integration for AirWatch skal du udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-airwatch-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-airwatch-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-airwatch-tutorial/IC749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-airwatch-tutorial/IC749322.png "Tilføj et program fra gallerry")

6.  I **søgefeltet**skal du skrive **AirWatch**.

    ![Galleri med programmet] (./media/active-directory-saas-airwatch-tutorial/IC791914.png "Galleri med programmet")

7.  Vælg **AirWatch**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![AirWatch] (./media/active-directory-saas-airwatch-tutorial/IC791915.png "AirWatch")
##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon

Formålet med dette afsnit er til at disponere hvordan du aktiverer brugere til at godkende AirWatch med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen.  
Du er påkrævet for at oprette en base-64 certifikatfil for kodet som en del af denne procedure.  
Hvis du ikke kender med denne procedure, se, [hvordan du konverterer et binært certifikat til en tekstfil](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **AirWatch** application integration.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-airwatch-tutorial/IC791916.png "Konfigurere Single Sign-On")

2.  Vælg **Microsoft Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge AirWatch** , og klik derefter på **Næste**.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-airwatch-tutorial/IC791917.png "Konfigurere Single Sign-On")

3.  Skriv din URL-adresse, der bruges af dine brugere til at logge på din AirWatch program i tekstfeltet **AirWatch Log på URL-adressen** på siden **Konfigurer App URL-adresse** (f.eks.: "*https:// companycode.awmdm.com/AirWatch/Login?gid=companycode*"), og klik derefter på **Næste**.

    ![Konfigurere App URL-adresse] (./media/active-directory-saas-airwatch-tutorial/IC791918.png "Konfigurere App URL-adresse")

4.  Klik på **Hent certifikat**på siden **Konfigurer single sign-on – på AirWatch** , og Gem derefter certifikatfilen på din computer.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-airwatch-tutorial/IC791919.png "Konfigurere Single Sign-On")

5.  Log på webstedet AirWatch virksomhed som administrator i et andet browservindue.

6.  Klik på **konti**i den venstre navigationsrude, og klik derefter på **Administratorer**.

    ![Administratorer] (./media/active-directory-saas-airwatch-tutorial/IC791920.png "Administratorer")

7.  Udvid menuen **Indstillinger** , og klik derefter på **Katalogtjenester**.

    ![Indstillinger] (./media/active-directory-saas-airwatch-tutorial/IC791921.png "Indstillinger")

8.  Klik på fanen **bruger** i **Base DN** textfield, Skriv dit domænenavn, og klik derefter på **Gem**.

    ![Bruger] (./media/active-directory-saas-airwatch-tutorial/IC791922.png "Bruger")

9.  Klik på fanen **Server** .

    ![Server] (./media/active-directory-saas-airwatch-tutorial/IC791923.png "Server")

10. Udfør følgende trin:

    ![Overføre] (./media/active-directory-saas-airwatch-tutorial/IC791924.png "Overføre")

    1.  Som **Directory Type**skal du vælge **ingen**.
    2.  Vælg **Brug SAML til godkendelse**.
    3.  Hvis du vil overføre hentede certifikatet, skal du klikke på **Overfør**.

11. I sektionen **anmode om** at udføre følgende trin:

    ![Anmode om] (./media/active-directory-saas-airwatch-tutorial/IC791925.png "Anmode om")

    1.  Som **Anmode om Binding Type**skal du vælge **meddelelse**.
    2.  Kopiér værdien **Enkelt Sign-On URL-adressen** på Azure-klassisk portalen på **Konfigurer single sign-on – på Airwatch** dialogboksen siden, og Indsæt det i tekstfeltet **Identitet udbyder enkelt Log på URL-adresse** .
    3.  Vælg **E-mailadresse**, som **NameID Format**.
    4.  Klik på **Gem**.

12. Klik på fanen **bruger** igen.

    ![Bruger] (./media/active-directory-saas-airwatch-tutorial/IC791926.png "Bruger")

13. I sektionen **attributter** skal du udføre følgende trin:

    ![Attribut] (./media/active-directory-saas-airwatch-tutorial/IC791927.png "Attribut")

    1.  Skriv **http://schemas.microsoft.com/identity/claims/objectidentifier**i tekstfeltet **Objekt-id** .
    2.  I tekstfeltet **brugernavn** skal du skrive **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.
    3.  I tekstfeltet **Navn** skal du skrive **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.
    4.  Skriv **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**i tekstfeltet **Fornavn** .
    5.  Skriv **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**i tekstfeltet **Efternavn** .
    6.  I tekstfeltet **mail** skal du skrive **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.
    7.  Klik på **Gem**.

14. Vælge enkelt sign-on – konfiguration bekræftelsen på Azure klassisk-portalen, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** .

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-airwatch-tutorial/IC791928.png "Konfigurere Single Sign-On")
##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af

For at aktivere Azure AD-brugere at logge på AirWatch, skal de klargøres til AirWatch.  
Klargøring af er AirWatch, en manuel opgave.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Hvis du vil klargøre en brugerkonti, skal du udføre følgende trin:

1.  Log på webstedet **AirWatch** virksomhed som administrator.

2.  Klik på **konti**i navigationsruden i venstre side, og klik derefter på **brugere**.

    ![Brugere] (./media/active-directory-saas-airwatch-tutorial/IC791929.png "Brugere")

3.  Klik på **Listen Vis**i menuen **brugere** , og klik derefter på **Tilføj \> Tilføj bruger**.

    ![Tilføj bruger] (./media/active-directory-saas-airwatch-tutorial/IC791930.png "Tilføj bruger")

4.  I dialogboksen **Tilføj / redigere bruger** skal du udføre følgende trin:

    ![Tilføj bruger] (./media/active-directory-saas-airwatch-tutorial/IC791931.png "Tilføj bruger")

    1.  Skriv **brugernavn**, **din adgangskode**, **Bekræft adgangskode**, **Fornavn**, **Efternavn**, **E-mail-adresse** for en gyldig Azure Active Directory-konto, du vil klargøre i den relaterede tekstbokse.
    2.  Klik på **Gem**.

>[AZURE.NOTE] Du kan bruge en hvilken som helst anden AirWatch bruger konto værktøjer til oprettelse eller API'er, som AirWatch til klargøring AAD brugerkonti.

##<a name="assigning-users"></a>Tildele brugere

Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-airwatch-perform-the-following-steps"></a>Hvis du vil tildele brugere til AirWatch, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.

2.  Klik på **Tildel brugere**på siden **AirWatch **application integration.

    ![Tildele brugere] (./media/active-directory-saas-airwatch-tutorial/IC791932.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Ja] (./media/active-directory-saas-airwatch-tutorial/IC767830.png "Ja")

Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang. Se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)kan finde flere oplysninger om panelet adgang.

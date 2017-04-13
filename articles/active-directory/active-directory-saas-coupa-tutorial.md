<properties 
    pageTitle="Selvstudium: Azure Active Directory-integration med Coupa | Microsoft Azure" 
    description="Lær, hvordan du bruger Coupa med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!" 
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

#<a name="tutorial-azure-active-directory-integration-with-coupa"></a>Selvstudium: Azure Active Directory-integration med Coupa

Formålet med dette selvstudium er at vise integration af Azure og Coupa.  
Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   En Coupa single sign-on aktiveret abonnement

Når du har fuldført dette selvstudium, vil de Azure AD-brugere, du har tildelt til Coupa kunne enkelt Log på det program, ved hjælp af [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md).

Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integrationen for Coupa
2.  Konfigurere Enkeltlogon
3.  Konfiguration af brugerklargøring af
4.  Tildele brugere

![Scenarie] (./media/active-directory-saas-coupa-tutorial/IC791897.png "Scenarie")
##<a name="enabling-the-application-integration-for-coupa"></a>Aktivere programmet integrationen for Coupa

Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integrationen for Coupa.

###<a name="to-enable-the-application-integration-for-coupa-perform-the-following-steps"></a>For at aktivere programmet integration for Coupa skal du udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-coupa-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-coupa-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-coupa-tutorial/IC749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-coupa-tutorial/IC749322.png "Tilføj et program fra gallerry")

6.  I **søgefeltet**skal du skrive **Coupa**.

    ![Galleri med programmet] (./media/active-directory-saas-coupa-tutorial/IC791898.png "Galleri med programmet")

7.  Vælg **Coupa**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![Coupa] (./media/active-directory-saas-coupa-tutorial/IC791899.png "Coupa")
##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon

Formålet med dette afsnit er til at disponere hvordan du aktiverer brugere til at godkende Coupa med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen.  
Konfiguration af single sign-on til Coupa, skal du hente en miniatureværdi fra et certifikat.  
Hvis du ikke kender med denne procedure, se, [hvordan du hente et certifikat miniature værdi](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Logge webstedet Coupa virksomhed som administrator.

2.  Gå til **konfiguration \> sikkerhedskontrol**.

    ![Sikkerhedskontrol] (./media/active-directory-saas-coupa-tutorial/IC791900.png "Sikkerhedskontrol")

3.  Hvis du vil hente filen Coupa metadata til din computer, skal du klikke på **Download og importere SP metadata**.

    ![Coupa SP metadata] (./media/active-directory-saas-coupa-tutorial/IC791901.png "Coupa SP metadata")

4.  Log portalen Azure klassisk i et andet browservindue.

5.  På siden **Coupa** application integration, klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** .

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-coupa-tutorial/IC791902.png "Konfigurere Single Sign-On")

6.  Vælg **Microsoft Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge Coupa** , og klik derefter på **Næste**.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-coupa-tutorial/IC791903.png "Konfigurere Single Sign-On")

7.  På siden **Konfigurer App URL-adresse** skal du udføre følgende trin:

    ![Konfigurere App URL-adresse] (./media/active-directory-saas-coupa-tutorial/IC791904.png "Konfigurere App URL-adresse")

    1.  I tekstfeltet **Log på URL-adresse** skal du skrive URL-adresse bruges af dine brugere til at logge din Coupa program (f.eks.: "*http://company.Coupa.com*").
    2.  Åbne den hentede fil til Coupa metadata, og Kopier **AssertionConsumerService indeks/URL-adresse**.
    3.  Indsæt værdien **AssertionConsumerService indeks/URL-adressen** i tekstfeltet **Coupa svar URL-adresse** .
    4.  Klik på **Næste**.

8.  Klik på **Hent metadata**for at hente metadatafilen, på siden **Konfigurer single sign-on – på Coupa** , og Gem derefter filen lokalt på din computer.

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-coupa-tutorial/IC791905.png "Konfigurere Single Sign-On")

9.  Gå til på webstedet Coupa virksomhed **konfiguration \> sikkerhedskontrol**.

    ![Sikkerhedskontrol] (./media/active-directory-saas-coupa-tutorial/IC791900.png "Sikkerhedskontrol")

10. I sektionen **Log på med Coupa legitimationsoplysninger** skal du udføre følgende trin:

    ![Log på med Coupa legitimationsoplysninger] (./media/active-directory-saas-coupa-tutorial/IC791906.png "Log på med Coupa legitimationsoplysninger")

    1.  Vælg **Log på med SAML**.
    2.  Klik på **Gennemse** for at overføre filen hentede Azure Active metadata.
    3.  Klik på **Gem**.

11. Vælge enkelt sign-on – konfiguration bekræftelsen på Azure klassisk-portalen, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** .

    ![Konfigurere Single Sign-On] (./media/active-directory-saas-coupa-tutorial/IC791907.png "Konfigurere Single Sign-On")
##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af

For at aktivere Azure AD-brugere at logge på Coupa, skal de klargøres til Coupa.  
Klargøring af er Coupa, en manuel opgave.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>For at konfigurere brugerklargøring skal du udføre følgende trin:

1.  Log på webstedet **Coupa** virksomhed som administrator.

2.  Klikke på **installationen**er i menuen øverst, og klik derefter på **brugere**.

    ![Brugere] (./media/active-directory-saas-coupa-tutorial/IC791908.png "Brugere")

3.  Klik på **Opret**.

    ![Oprette brugere] (./media/active-directory-saas-coupa-tutorial/IC791909.png "Oprette brugere")

4.  I sektionen **Brugeren oprette** skal du udføre følgende trin:

    ![Oplysninger om bruger] (./media/active-directory-saas-coupa-tutorial/IC791910.png "Oplysninger om bruger")

    1.  Skriv **logon**, **Fornavn**, **Efternavn**, **Enkelt Sign-On-ID**, **mail** attributterne for en gyldig Azure Active Directory-konto, du vil klargøre i den relaterede tekstbokse.
    2.  Klik på **Opret**.

    >[AZURE.NOTE] Azure Active Directory-kontoindehaver får en mail med et link for at bekræfte kontoen, før den bliver aktiv.

>[AZURE.NOTE] Du kan bruge en hvilken som helst anden Coupa bruger konto værktøjer til oprettelse eller API'er, som Coupa til klargøring AAD brugerkonti.

##<a name="assigning-users"></a>Tildele brugere

Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-coupa-perform-the-following-steps"></a>Hvis du vil tildele brugere til Coupa, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.

2.  Klik på **Tildel brugere**på siden **Coupa **application integration.

    ![Tildele brugere] (./media/active-directory-saas-coupa-tutorial/IC791911.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Ja] (./media/active-directory-saas-coupa-tutorial/IC767830.png "Ja")

Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang. Se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)kan finde flere oplysninger om panelet adgang.

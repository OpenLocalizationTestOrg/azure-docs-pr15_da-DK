<properties 
    pageTitle="Selvstudium: Azure Active Directory-integration med Kudos | Microsoft Azure" 
    description="Lær, hvordan du bruger Kudos med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!" 
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

#<a name="tutorial-azure-active-directory-integration-with-kudos"></a>Selvstudium: Azure Active Directory-integration med Kudos
  
Formålet med dette selvstudium er at vise integration af Azure og Kudos.  
Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   En Kudos lejer
  
Når du har fuldført dette selvstudium, vil de Azure AD-brugere, du har tildelt til Kudos kunne enkelt Log på programmet på webstedet Kudos virksomhed (service provider startet sign-on) eller ved hjælp af [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md).
  
Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integrationen for Kudos
2.  Konfigurere Enkeltlogon
3.  Konfiguration af brugerklargøring af
4.  Tildele brugere

![Scenarie] (./media/active-directory-saas-kudos-tutorial/IC787799.png "Scenarie")
##<a name="enabling-the-application-integration-for-kudos"></a>Aktivere programmet integrationen for Kudos
  
Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integrationen for Kudos.

###<a name="to-enable-the-application-integration-for-kudos-perform-the-following-steps"></a>For at aktivere programmet integration for Kudos skal du udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-kudos-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-kudos-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-kudos-tutorial/IC749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-kudos-tutorial/IC749322.png "Tilføj et program fra gallerry")

6.  I **søgefeltet**skal du skrive **Kudos**.

    ![Galleri med programmet] (./media/active-directory-saas-kudos-tutorial/IC787800.png "Galleri med programmet")

7.  Vælg **Kudos**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![Kudos] (./media/active-directory-saas-kudos-tutorial/IC787801.png "Kudos")
##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon
  
Formålet med dette afsnit er til at disponere hvordan du aktiverer brugere til at godkende Kudos med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen.  
Du er påkrævet for at oprette en base-64 certifikatfil for kodet som en del af denne procedure.  
Hvis du ikke kender med denne procedure, se, [hvordan du konverterer et binært certifikat til en tekstfil](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **Kudos** application integration.

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-kudos-tutorial/IC787802.png "Konfigurere Enkeltlogon")

2.  Vælg **Microsoft Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge Kudos** , og klik derefter på **Næste**.

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-kudos-tutorial/IC787803.png "Konfigurere Enkeltlogon")

3.  Skriv din URL-adresse ved hjælp af følgende mønster "*https://company.kudosnow.com*" på siden **Konfigurer App URL-adressen** i tekstfeltet **Kudos Log på URL-adressen** , og klik derefter på **Næste**.

    ![Konfigurere App URL-adresse] (./media/active-directory-saas-kudos-tutorial/IC787804.png "Konfigurere App URL-adresse")

4.  Klik på **Hent certifikat**på siden **Konfigurer single sign-on – på Kudos** , og Gem derefter certifikatfilen på din computer.

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-kudos-tutorial/IC787805.png "Konfigurere Enkeltlogon")

5.  Log på din Kudos virksomheds websted som en administrator i et andet browservindue.

6.  Klik på **Indstillinger**i menuen øverst.

    ![Indstillinger] (./media/active-directory-saas-kudos-tutorial/IC787806.png "Indstillinger")

7.  Klik på **integrationer \> SSO**.

8.  I sektionen **SSO** skal du udføre følgende trin:

    ![SSO] (./media/active-directory-saas-kudos-tutorial/IC787807.png "SSO")

    1.  Kopiér værdien **Enkelt Sign-On URL-adressen** på Azure-klassisk portalen på **Konfigurer single sign-on – på Kudos** dialogboksen siden, og Indsæt det i tekstfeltet **logge på URL-adresse** .
    2.  Oprette en **base-64-kodet** fil fra dit hentede certifikat.  

        >[AZURE.TIP]
        Du kan finde flere oplysninger, se, [hvordan du konverterer et binært certifikat til en tekstfil](http://youtu.be/PlgrzUZ-Y1o)

    3.  Åbn dit base-64-kodet certifikat i Notesblok, kopiere indholdet af den i Udklipsholder og sætte den ind på tekstfeltet **x.509-certifikat**
    4.  Kopiér værdien **Enkelt Sign-Out URL-adressen** på Azure-klassisk portalen på **Konfigurer single sign-on – på Kudos** dialogboksen siden, og Indsæt det i tekstfeltet **Log URL-adresse** .
    5.  Skriv virksomhedens navn i tekstfeltet **Dine Kudos URL-adresse** .
    6.  Klik på **Gem**.

9.  Vælge enkelt sign-on – konfiguration bekræftelsen på Azure klassisk-portalen, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** .

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-kudos-tutorial/IC787808.png "Konfigurere Enkeltlogon")
##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af
  
For at aktivere Azure AD-brugere at logge på Kudos, skal de klargøres til Kudos.  
Klargøring af er Kudos, en manuel opgave.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Hvis du vil klargøre en brugerkonti, skal du udføre følgende trin:

1.  Log på webstedet **Kudos** virksomhed som administrator.

2.  Klik på **Indstillinger**i menuen øverst.

    ![Indstillinger] (./media/active-directory-saas-kudos-tutorial/IC787806.png "Indstillinger")

3.  Klik på **bruger administrator**.

4.  Klik på fanen **brugere** , og klik derefter på **Tilføj en bruger**.

    ![Bruger administrator] (./media/active-directory-saas-kudos-tutorial/IC787809.png "Bruger administrator")

5.  I sektionen **Tilføj en bruger** skal du udføre følgende trin:

    ![Tilføje en bruger] (./media/active-directory-saas-kudos-tutorial/IC787810.png "Tilføje en bruger")

    1.  Skriv **Fornavn**, **Efternavn**, **mail** og andre detaljer for en gyldig Azure Active Directory-konto, du vil klargøre i den relaterede tekstbokse.
    2.  Klik på **Opret bruger**.

>[AZURE.NOTE]Du kan bruge en hvilken som helst anden Kudos bruger konto værktøjer til oprettelse eller API'er, som Kudos til klargøring AAD brugerkonti.

##<a name="assigning-users"></a>Tildele brugere
  
Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-kudos-perform-the-following-steps"></a>Hvis du vil tildele brugere til Kudos, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.

2.  Klik på **Tildel brugere**på siden **Kudos **application integration.

    ![Tildele brugere] (./media/active-directory-saas-kudos-tutorial/IC787811.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Ja] (./media/active-directory-saas-kudos-tutorial/IC767830.png "Ja")
  
Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang. Se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)kan finde flere oplysninger om panelet adgang.
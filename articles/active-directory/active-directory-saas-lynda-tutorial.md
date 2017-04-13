<properties 
    pageTitle="Selvstudium: Azure Active Directory-integration med Lynda.com | Microsoft Azure" 
    description="Lær, hvordan du bruger Lynda.com med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!" 
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

#<a name="tutorial-azure-active-directory-integration-with-lyndacom"></a>Selvstudium: Azure Active Directory-integration med Lynda.com
  
Formålet med dette selvstudium er at vise integration af Azure og Lynda.com.  
Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   En Lynda.com lejer
  
Når du har fuldført dette selvstudium, vil de Azure AD-brugere, du har tildelt til Lynda.com kunne enkelt Log på programmet på webstedet Lynda.com virksomhed (service provider startet sign-on) eller ved hjælp af [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md).
  
Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integrationen for Lynda.com
2.  Konfigurere Enkeltlogon
3.  Konfiguration af brugerklargøring af
4.  Tildele brugere

![Scenarie] (./media/active-directory-saas-lynda-tutorial/IC781046.png "Scenarie")
##<a name="enabling-the-application-integration-for-lyndacom"></a>Aktivere programmet integrationen for Lynda.com
  
Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integrationen for Lynda.com.

###<a name="to-enable-the-application-integration-for-lyndacom-perform-the-following-steps"></a>For at aktivere programmet integration for Lynda.com skal du udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-lynda-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-lynda-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-lynda-tutorial/IC749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-lynda-tutorial/IC749322.png "Tilføj et program fra gallerry")

6.  I **søgefeltet**skal du skrive **Lynda.com**.

    ![Galleri med programmet] (./media/active-directory-saas-lynda-tutorial/IC777524.png "Galleri med programmet")

7.  Vælg **Lynda.com**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![Lynda.com] (./media/active-directory-saas-lynda-tutorial/IC777525.png "Lynda.com")
##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon
  
Formålet med dette afsnit er til at disponere hvordan du aktiverer brugere til at godkende Lynda.com med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen.

>[AZURE.IMPORTANT]For at kunne konfigurere single sign-on på din Lynda.com lejer, skal du kontakte først Lynda.com teknisk support for at få denne funktion er aktiveret.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **Lynda.com** application integration.

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-lynda-tutorial/IC777526.png "Konfigurere Enkeltlogon")

2.  Vælg **Microsoft Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge Lynda.com** , og klik derefter på **Næste**.

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-lynda-tutorial/IC777527.png "Konfigurere Enkeltlogon")

3.  Skriv din Lynda.com lejer URL-adresse i tekstfeltet **Lynda.com log i URL-adresse** på siden **Konfigurer App URL-adresse** (f.eks.: *https://shib.lynda.com/Shibboleth.sso/InCommon?providerId=https://sts.windows-ppe.net/6247032d-9415-403c-b72b-277e3fb6f2c8/&target= https://shib.lynda.com/InCommon*), og klik derefter på **Næste**.

    ![Konfigurere app URL-adresse] (./media/active-directory-saas-lynda-tutorial/IC781047.png "Konfigurere app URL-adresse")

4.  Klik på **Hent metadata**for at hente metadata for din, på siden **Konfigurer single sign-on – på Lynda.com** , og Gem derefter certifikatfilen lokalt på din computer.

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-lynda-tutorial/IC777529.png "Konfigurere Enkeltlogon")

5.  Sende metadatafilen hentede til Lynda.com-supportteam. Supportteam Lynda.com understøtter Single Sign-On-konfiguration for dig.

6.  Vælge enkelt sign-on – konfiguration bekræftelsen på Azure klassisk-portalen, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** .

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-lynda-tutorial/IC777530.png "Konfigurere Enkeltlogon")
##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af
  
Der er ingen handlingspunkt for dig at konfigurere bruger klargøring til Lynda.com.  
Når en tildelt brugeren forsøger at logge på ved hjælp af panelet access Lynda.com, kontrollerer Lynda.com om brugeren findes.  
Hvis der findes ingen brugerkonto endnu, oprettes den automatisk ved Lynda.com.

>[AZURE.NOTE]Du kan bruge en hvilken som helst anden Lynda.com bruger konto værktøjer til oprettelse eller API'er, som Lynda.com til klargøring AAD brugerkonti.

##<a name="assigning-users"></a>Tildele brugere
  
Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-lyndacom-perform-the-following-steps"></a>Hvis du vil tildele brugere til Lynda.com, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.

2.  Klik på **Tildel brugere**på siden **Lynda.com **application integration.

    ![Tildele brugere] (./media/active-directory-saas-lynda-tutorial/IC777531.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Ja] (./media/active-directory-saas-lynda-tutorial/IC767830.png "Ja")
  
Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang. Se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)kan finde flere oplysninger om panelet adgang.
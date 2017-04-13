<properties 
    pageTitle="Selvstudium: Azure Active Directory-integration med Dropbox til erhverv | Microsoft Azure" 
    description="Lær, hvordan du bruger Dropbox for Business med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!" 
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
    ms.date="08/16/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-dropbox-for-business"></a>Selvstudium: Azure Active Directory-integration med Dropbox til erhverv
  
Formålet med dette selvstudium er at vise integration af Azure og Dropbox til erhverv.  
Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   En test lejer i Dropbox til erhverv
  
Når du har fuldført dette selvstudium, Firmaoplysninger de Azure AD-brugere, du har tildelt til Dropbox til Business vil kunne enkelt Log på programmet på din Dropbox til erhverv-websted (service provider startet sign-on) eller ved hjælp af [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md).
  
Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integrationen dropbox til virksomheder
2.  Konfigurere Enkeltlogon
3.  Konfiguration af brugerklargøring af
4.  Tildele brugere

![Scenarie] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769508.png "Scenarie")



##<a name="enabling-the-application-integration-for-dropbox-for-business"></a>Aktivere programmet integrationen dropbox til virksomheder
  
Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integrationen for Dropbox til erhverv.

###<a name="to-enable-the-application-integration-for-dropbox-for-business-perform-the-following-steps"></a>For at aktivere programmet integration for Dropbox til erhverv skal du udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC749322.png "Tilføj et program fra gallerry")

6.  I **søgefeltet**skal du skrive **Dropbox til erhverv**.

    ![Galleri med programmet] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC701010.png "Galleri med programmet")

7.  Vælg **Dropbox til erhverv**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![Dropbox til erhverv] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC701011.png "Dropbox til erhverv")

##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon
  
Formålet med dette afsnit er til at disponere hvordan du aktiverer brugere til at godkende Dropbox til erhverv med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen.

Du er påkrævet for at overføre et base-64-kodet certifikat til dine Dropbox for Business-lejer som en del af denne procedure. Hvis du ikke kender med denne procedure, se, [hvordan du konverterer et binært certifikat til en tekstfil](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  Klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** i Azure klassisk portalen, på siden **Dropbox til erhverv** application integration.

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC749323.png "Konfigurere Enkeltlogon")

2.  Vælg **Microsoft Azure AD Single Sign-On**på siden **hvordan vil du brugere til at logge Dropbox til erhverv** , og klik derefter på **Næste**.

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC749327.png "Konfigurere Enkeltlogon")

3.  På siden **Konfigurer App URL-adresse** skal du udføre følgende trin:

    en. Sign-on til dine Dropbox for business-lejer. 

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769509.png "Konfigurere Enkeltlogon")

    b. Klik på **Administrationskonsollen**i navigationsruden i venstre side. 

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769510.png "Konfigurere Enkeltlogon")

    c. Klik på **godkendelse** i den venstre navigationsrude i **Administrationskonsollen**. 

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769511.png "Konfigurere Enkeltlogon")

    d. Vælg **Aktiver enkeltlogon**i sektionen **Single sign-on** , og klik derefter på **flere** for at udvide denne sektion.  

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769512.png "Konfigurere Enkeltlogon")

    e. Kopiér URL-adressen ud for **brugerne kan logge på ved at indtaste deres mailadresse eller de kan gå direkte til**. 

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769513.png "Konfigurere Enkeltlogon")

    f. Indsæt URL-adressen på Azure klassisk-portalen, i tekstfeltet **DropBox til erhverv logge på** URL-adresse. 

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769514.png "Konfigurere Enkeltlogon")  



4. Klik på **Hent certifikat**på siden **Konfigurer single sign-on – på Dropbox til erhverv** , og Gem derefter certifikatfilen på din computer.  

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769515.png "Konfigurere Enkeltlogon")


5. På din Dropbox til Business-lejeren, i afsnittet **enkeltlogon** i siden **godkendelse** skal du udføre følgende trin: 

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769516.png "Konfigurere Enkeltlogon")

    en. Klik på **påkrævet**.

    b. Kopiér værdien **logon URL-adressen** på Azure-klassisk portalen på **konfigurere Enkeltlogon i Dropbox til erhverv** dialogboksen siden, og Indsæt det i tekstfeltet **logge på URL-adresse** .


    c. Oprette en **Base-64-kodet** fil fra dit hentede certifikat. 

    > [AZURE.TIP] Du kan finde flere oplysninger, se, [hvordan du konverterer et binært certifikat til en tekstfil](http://youtu.be/PlgrzUZ-Y1o).


    d. Klik på knappen **"Vælg certifikat"** , og gå derefter til din **base-64-kodet certifikatfil**.


    e. Klik på knappen **"Gem ændringer"** for at fuldføre konfigurationen på din DropBox for Business-lejer.


6. Vælge enkelt sign-on – konfiguration bekræftelsen på Azure klassisk-portalen, og klik derefter på **udført** for at lukke dialogboksen **Konfigurere Single Sign-On** . 

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC749329.png "Konfigurere Enkeltlogon")



##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af
  
Formålet med dette afsnit er til at disponere hvordan du aktiverer brugerklargøring af Active Directory-brugerkonti til Dropbox til erhverv.


### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>For at konfigurere brugerklargøring skal du udføre følgende trin:

1. Klik på **Konfigurer brugerklargøring** for at åbne dialogboksen **Konfigurer brugerklargøring** i Azure klassisk portalen, på siden **Dropbox til erhverv** application integration.

2. Aktivér bruger klargøringen til DropBox for Business-side, klik på Aktivér brugerklargøring for at åbne Log på til Dropbox for at oprette et link med Azure AD-dialogboksen.  

    ![Klargøring af bruger] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769517.png "Klargøring af bruger")

3. Log på din Dropbox for Business-lejer på dialogboksen **logon på Dropbox for at oprette et link med Azure AD** . 

    ![Klargøring af bruger] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769518.png "Klargøring af bruger")



4. Klik på **Tillad** for at give Azure AD til at få adgang til Dropbox. 

    ![Klargøring af bruger] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769519.png "Klargøring af bruger")



5. Klik på knappen **udført** for at afslutte konfigurationen.  

    ![Klargøring af bruger] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769520.png "Klargøring af bruger")




##<a name="assigning-users"></a>Tildele brugere
  
Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-dropbox-for-business-perform-the-following-steps"></a>Hvis du vil tildele brugere til Dropbox til erhverv, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.

2.  Klik på **Tildel brugere**på siden **Dropbox til erhverv **application integration.

    ![Tildele brugere] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769521.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Ja] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC767830.png "Ja")
  


Du bør nu vente 10 minutter og bekræfte, at kontoen er blevet synkroniseret med Dropbox til erhverv.

Du kan kontrollere klargøring status, ved at klikke på **Dashboard** i **Dropbox til erhverv** programmets integrationsside på portalen Azure klassisk som et første trin i bekræftelse.

![Tildele brugere] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769522.png "Tildele brugere")


En korrekt færdige bruger klargøring cyklus er angivet med en relateret status.

![Tildele brugere] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769523.png "Tildele brugere")


Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang.
Se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)kan finde flere oplysninger om panelet adgang.




## <a name="additional-resources"></a>Yderligere ressourcer

* [Liste over selvstudier om, hvordan du integrerer SaaS Apps med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Hvad er adgang til programmer og single sign-on med Azure Active Directory?](active-directory-appssoaccess-whatis.md)
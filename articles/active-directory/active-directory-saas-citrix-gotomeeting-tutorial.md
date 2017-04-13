<properties 
    pageTitle="Selvstudium: Azure Active Directory-integration med Citrix GoToMeeting | Microsoft Azure" 
    description="Lær at bruge Citrix GoToMeeting med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!." 
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

#<a name="tutorial-azure-active-directory-integration-with-citrix-gotomeeting"></a>Selvstudium: Azure Active Directory-integration med Citrix GoToMeeting  
Gælder for: Azure

>[AZURE.TIP]Feedback, ved at klikke [her](http://go.microsoft.com/fwlink/?LinkId=522412).

Formålet med dette selvstudium er at vise integration af Azure og Citrix GoToMeeting. Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   En lejer i Citrix GoToMeeting

Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integration til Citrix GoToMeeting
2.  Konfigurere Enkeltlogon
3.  Konfiguration af brugerklargøring af
4.  Tildele brugere

![Konfiguration] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768996.png "Konfiguration")



##<a name="enabling-the-application-integration-for-citrix-gotomeeting"></a>Aktivere programmet integration til Citrix GoToMeeting

Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integration til Citrix GoToMeeting.

###<a name="to-enable-the-application-integration-for-citrix-gotomeeting-perform-the-following-steps"></a>For at aktivere programmet integration for Citrix GoToMeeting skal du udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC749321.png "Tilføj program")

5.  Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program i galleriet**.

    ![Tilføj et program fra galleriet] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC749322.png "Tilføj et program fra galleriet")

6.  I **søgefeltet**skal du skrive **Citrix GoToMeeting**.

    ![Citrix GoToMeeting] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC701006.png "Citrix GoToMeeting")

7.  Vælg **Citrix GoToMeeting**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![Citrix GoToMeeting] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC701012.png "Citrix GoToMeeting")
##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon

Formålet med dette afsnit er til at disponere hvordan du aktiverer brugere til at godkende Citrix GoToMeeting med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen.  
Du er påkrævet for at overføre et base-64-kodet certifikat til din Citrix GoToMeeting lejer som en del af denne procedure.  
Hvis du ikke kender med denne procedure, se, [hvordan du konverterer et binært certifikat til en tekstfil](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  På siden **Citrix GoToMeeting** application integration, klik på **Konfigurer single sign-on –** for at åbne dialogboksen **KONFIGURER enkelt Log på** .

    ![Aktivere enkeltlogon] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768997.png "Aktivere enkeltlogon")

2.  På siden **hvordan vil du brugere til at logge Citrix GoToMeeting** skal du vælge **Microsoft Azure AD Single Sign-On**.

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768998.png "Konfigurere Enkeltlogon")


3. Klik på **Næste**på siden **Konfigurer App-indstillinger** . 

    ![Aktivere enkeltlogon] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC7689981.png "Aktivere enkeltlogon")

4.  Klik på **Hent certifikat**på siden **Konfigurer single sign-on – på Citrix GoToMeeting** , og Gem derefter certifikatfilen på din computer.

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768999.png "Konfigurere Enkeltlogon")

5.  Log på din Citrix organisation Center ([https://account.citrixonline.com/organization/administration/](https://account.citrixonline.com/organization/administration/)) i et andet browservindue.

6. Klik på fanen **Identitetsudbyder** , og derefter udføre følgende trin:  

    ![SAML konfiguration] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC6892321.png "SAML konfiguration")

    en. Vælg **Manuel**

    
    b. Kopiér værdien **Logon URL-adressen** på Azure-klassisk portalen på **Konfigurer single sign-on – på Citrix GoToMeeting** dialogboksen siden, og Indsæt det i tekstfeltet **logon URL-adressen** . 

    
    c. Kopiér værdien **Sign-Out URL-adressen** på Azure-klassisk portalen på **Konfigurer single sign-on – på Citrix GoToMeeting** dialogboksen siden, og Indsæt det i tekstfeltet **hvor URL-adressen** .

    
    d. Kopiér værdien **Enheds-ID** i Azure klassisk-portalen på **Konfigurer single sign-on – på Citrix GoToMeeting** dialogboksen siden, og Indsæt det i tekstfeltet **Identitet udbyder enheds-ID** .

   
    e. Hvis du vil overføre dit hentede certifikat, skal du klikke på **Overfør certifikat**.

    
    f. Klik på **Gem**.

6.  Vælge enkelt sign-on – konfiguration bekræftelsen på Azure klassisk-portalen, og klik derefter på **Næste**.

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769000.png "Konfigurere Enkeltlogon")


7. **Enkelt sign-on –** bekræftelsessiden, klik på **udført**.

    ![SAML konfiguration] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC7689982.png "SAML konfiguration")





##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af

Formålet med dette afsnit er til at disponere hvordan du aktiverer klargøring af Active Directory-brugerkonti til Citrix GoToMeeting.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>For at konfigurere brugerklargøring skal du udføre følgende trin:

1.  Klik på **Konfigurer brugerklargøring** for at åbne dialogboksen **Konfigurer brugerklargøring** i Azure klassisk portalen, på siden **Citrix GoToMeeting** application integration.

    ![Konfigurer brugerklargøring] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769001.png "Konfigurer brugerklargøring")

2.  På siden **Indstillinger og Administratoroplysninger** skal du udføre følgende trin:

    ![Konfigurer brugerklargøring] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769002.png "Konfigurer brugerklargøring")

    en. Skriv navnet på en administrator i tekstfeltet **Brugernavn Citrix GoToMeeting administrator** .

    
    b. I tekstfeltet **Citrix GoToMeeting administratoradgangskode** skal administratorens adgangskode.

    
    c. Klik på **Næste**.

3.  Klik på markering for at gemme din konfiguration på siden **Confirmation** .

4.  Klik på knappen **Valider** for at bekræfte din konfiguration.


##<a name="assigning-users"></a>Tildele brugere

Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-citrix-gotomeeting-perform-the-following-steps"></a>Hvis du vil tildele brugere til Citrix GoToMeeting, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.

2.  Klik på **Tildel brugere**på siden **Citrix GoToMeeting** application integration.

    ![Tildele brugere] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769003.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Ja] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC767830.png "Ja")

Du bør nu vente 10 minutter og bekræfte, at kontoen er blevet synkroniseret med Dropbox til erhverv.

Du kan kontrollere klargøring status, ved at klikke på Dashboard i D på siden **Citrix GoToMeeting** application integration på portalen Azure klassisk som et første trin i bekræftelse.

![Dashboard] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769004.png "Dashboard")

En korrekt færdige bruger klargøring cyklus er angivet med en relateret status:

![Integration af status] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769005.png "Integration af status")

Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang.

Se [Introduktion til panelet adgang](https://msdn.microsoft.com/library/dn308586)kan finde flere oplysninger om panelet adgang.

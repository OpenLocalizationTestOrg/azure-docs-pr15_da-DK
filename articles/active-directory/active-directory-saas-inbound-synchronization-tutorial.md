<properties 
    pageTitle="Selvstudium: Konfiguration af arbejdsdag til indgående synkronisering | Microsoft Azure" 
    description="Lær, hvordan du bruger indgående synkronisering med Azure Active Directory for at aktivere enkeltlogon, automatiseret klargøring og mere!" 
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
    ms.date="04/06/2016" 
    ms.author="jeedes" />

#<a name="tutorial-configuring-workday-for-inbound-synchronization"></a>Selvstudium: Konfiguration af arbejdsdag til indgående synkronisering
>[AZURE.NOTE]Azure Active Directory (AD) Premium er tilgængelig for kunder i Kina, der bruger den i hele verden forekomst af Azure AD.    
Azure AD-Premium understøttes ikke i øjeblikket i tjenesten Microsoft Azure drevet af 21Vianet i Kina.    

Formålet med dette selvstudium er så du kan se de trin, du skal udføre i arbejdsdag og Microsoft Azure AD til at importere personer fra arbejdsdag til Microsoft Azure AD.    
 Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:  

-   Et gyldigt Azure-abonnement  
-   En lejer i arbejdsdag  

Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:  

1.  Aktivere programmet integrationen for arbejdsdag  
2.  Oprette en integration af bruger  
3.  Oprette en sikkerhedsgruppe  
4.  Tildele integration systembrugeren til sikkerhedsgruppen  
5.  Konfigurere sikkerhedsindstillinger for gruppe  
6.  Aktivering af sikkerhed politikændringer  
7.  Import af konfiguration af brugers i Microsoft Azure AD  

##<a name="enabling-the-application-integration-for-workday"></a>Aktivere programmet integrationen for arbejdsdag

Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integration til Salesforce.    

###<a name="to-enable-the-application-integration-for-workday-perform-the-following-steps"></a>For at aktivere programmet integration for arbejdsdag skal du udføre følgende trin:

1.  Klik på **Active Directory**i portalen Azure administration i den venstre navigationsrude.    

    ![Active Directory] (./media/active-directory-saas-inbound-synchronization-tutorial/IC700993.png "Active Directory")  

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .    

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.    

    ![Programmer] (./media/active-directory-saas-inbound-synchronization-tutorial/IC700994.png "Programmer")  

4.  Klik på **Tilføj en App**for at åbne **Programmet galleriet**, og klik derefter på **Tilføj et program for organisationen at bruge**.    

    ![Hvad vil du gøre?] (./media/active-directory-saas-inbound-synchronization-tutorial/IC700995.png "Hvad vil du gøre?")  

5.  I **søgefeltet**skal du skrive **arbejdsdag**.    

    ![Arbejdsdag] (./media/active-directory-saas-inbound-synchronization-tutorial/IC701021.png "Arbejdsdag")  

6.  Vælg **arbejdsdag**i resultatruden, og klik derefter på **udført** for at tilføje programmet.    

    ![Arbejdsdag] (./media/active-directory-saas-inbound-synchronization-tutorial/IC701022.png "Arbejdsdag")  

##<a name="creating-an-integration-system-user"></a>Oprette en integration af bruger

1.  Angiv **brugeroprettelsen** i søgefeltet i **Arbejdsdag Workbench**, og derefter klikke på linket, **Opret Integration systembruger**.     

    ![oprette bruger] (./media/active-directory-saas-inbound-synchronization-tutorial/IC750979.png "oprette bruger")  

2.  Fuldføre opgaven Opret Integration systembruger ved at angive et brugernavn og adgangskode til en ny Integration systembruger.  Lad kræver ny adgangskode ved næste Log på indstilling ikke er markeret, fordi denne bruger vil være logge på ved hjælp af programmering.    
    Du kan lade Session Timeout minutter med standardværdien 0, hvilket forhindrer, at brugerens sessioner timeout for tidligt.    

    ![Oprette Integration af bruger] (./media/active-directory-saas-inbound-synchronization-tutorial/IC750980.png "Oprette Integration af bruger")  

##<a name="creating-a-security-group"></a>Oprette en sikkerhedsgruppe

For dette scenario, der er beskrevet i dette selvstudium, skal du oprette en sikkerhedsgruppe begrænsninger integration system og tildele brugeren til den.    

1.  Angiv oprette sikkerhedsgruppen i søgefeltet, og klik derefter på linket, oprette sikkerhedsgruppe.     

    ![CreateSecurity gruppe] (./media/active-directory-saas-inbound-synchronization-tutorial/IC750981.png "CreateSecurity gruppe")  

2.  Fuldføre opgaven oprette sikkerhedsgruppe.  Vælg Integration System-sikkerhedsgruppe – ugenert rullelisten Type af lejet sikkerhedsgruppe til at oprette en sikkerhedsgruppe, som medlemmer eksplicit tilføjes.     

    ![CreateSecurity gruppe] (./media/active-directory-saas-inbound-synchronization-tutorial/IC750982.png "CreateSecurity gruppe")  

##<a name="assigning-the-integration-system-user-to-the-security-group"></a>Tildele integration systembrugeren til sikkerhedsgruppen

1.  Angiv Rediger sikkerhedsgruppe i søgefeltet, og klik på linket **Rediger sikkerhedsgruppe**.     

    ![Rediger sikkerhedsgruppe] (./media/active-directory-saas-inbound-synchronization-tutorial/IC750983.png "Rediger sikkerhedsgruppe")  

2.  Søge efter, og vælg den nye integration sikkerhedsgruppe efter navn    

    ![Rediger sikkerhedsgruppe] (./media/active-directory-saas-inbound-synchronization-tutorial/IC750984.png "Rediger sikkerhedsgruppe")  

3.  Føje den nye integration systembruger til den nye sikkerhedsgruppe.       

    ![System sikkerhedsgruppe] (./media/active-directory-saas-inbound-synchronization-tutorial/IC750985.png "System sikkerhedsgruppe")  

##<a name="configuring-security-group-options"></a>Konfigurere sikkerhedsindstillinger for gruppe

I dette trin skal give du til de nye gruppetilladelser for få og læg handlinger på de objekter, der er sikret af de følgende domæne sikkerhedspolitikker:  

-   Klargøring af eksterne konto  
-   Arbejder Data: Offentlige arbejder rapporter  
-   Arbejder Data: Alle placeringer  
-   Arbejder Data: Aktuelle personale oplysninger  
-   Arbejder Data: Business titel på arbejderprofil  

&nbsp;  

1.  Angiv domæne sikkerhedspolitikker i søgefeltet, og derefter klikke på linket, domæne sikkerhedspolitikker for funktionsområder.     

    ![Domæne sikkerhedspolitikker] (./media/active-directory-saas-inbound-synchronization-tutorial/IC750986.png "Domæne sikkerhedspolitikker")  

2.  Søg efter system, og vælg System funktionsområder.  Klik på knappen mærket, OK.     

    ![Domæne sikkerhedspolitikker] (./media/active-directory-saas-inbound-synchronization-tutorial/IC750987.png "Domæne sikkerhedspolitikker")  

3.  Udvid Administration af sikkerhed på listen over sikkerhedspolitikker for området System funktionelle, og vælg Domænesikkerhedspolitik, eksterne klargøring af konto.     

    ![Domæne sikkerhedspolitikker] (./media/active-directory-saas-inbound-synchronization-tutorial/IC750988.png "Domæne sikkerhedspolitikker")  

4.  Klik på knappen Rediger tilladelser, og Tilføj derefter den nye sikkerhedsgruppe til listen over sikkerhedsgrupper med Get og læg integration tilladelser på skærmbilledet Rediger tilladelser.     

    ![Redigere tilladelser] (./media/active-directory-saas-inbound-synchronization-tutorial/IC750989.png "Redigere tilladelser")  

5.  Gentag trin 1, ovenfor, at vende tilbage til skærmbilledet for at vælge funktionsområder, og denne gang skal søge efter personale, Vælg Staffing funktionsområder, og klik på knappen OK mærket.    

    ![Domæne sikkerhedspolitikker] (./media/active-directory-saas-inbound-synchronization-tutorial/IC750990.png "Domæne sikkerhedspolitikker")  

6.  På listen over sikkerhedspolitikker for området Staffing funktionelle, udvid arbejder Data: Staffing, og Gentag trin 4 ovenfor for hver af disse resterende sikkerhedspolitikker:    

    -   Arbejder Data: Offentlige arbejder rapporter  
    -   Arbejder Data: Alle placeringer  
    -   Arbejder Data: Aktuelle personale oplysninger  
    -   Arbejder Data: Business titel på arbejderprofil    

    ![Domæne sikkerhedspolitikker] (./media/active-directory-saas-inbound-synchronization-tutorial/IC750991.png "Domæne sikkerhedspolitikker")  

##<a name="activating-security-policy-changes"></a>Aktivering af sikkerhed politikændringer

1.  Angiv aktivere, i søgefeltet, og klik derefter på linket, aktivere ventende sikkerhed politikændringer.    

    ![Aktivere] (./media/active-directory-saas-inbound-synchronization-tutorial/IC750992.png "Aktivere")  

2.   Begynde at aktivere ventende sikkerhed politikændringer opgaven ved at angive en kommentar til overvågning og derefter klikke på knappen mærket, OK.      

    ![Aktivere afventer sikkerhed] (./media/active-directory-saas-inbound-synchronization-tutorial/IC750993.png "Aktivere afventer sikkerhed")  

3.  Fuldføre opgaven på det næste skærmbillede ved at markere afkrydsningsfeltet mærket Bekræft og klikke på knappen mærket, OK.     

    ![Aktivere afventer sikkerhed] (./media/active-directory-saas-inbound-synchronization-tutorial/IC750994.png "Aktivere afventer sikkerhed")  

##<a name="configuring-user-import-in-microsoft-azure-ad"></a>Import af konfiguration af brugers i Microsoft Azure AD

Formålet med dette afsnit er at oprette en disposition for at konfigurere Microsoft Azure AD til at importere personer fra arbejdsdag.    

###<a name="to-configure-user-import-in-microsoft-azure-ad-perform-the-following-steps"></a>For at konfigurere import af brugers i Microsoft Azure AD skal du udføre følgende trin:

1.  Klik på **Konfigurer bruger importere** for at åbne dialogboksen **Konfigurer klargøring** på siden **arbejdsdag** application integration.    

2.  Udfør følgende trin på siden **Indstillinger og Administratoroplysninger** , og klik derefter på Næste:    

    ![Indstillinger og Administratoroplysninger] (./media/active-directory-saas-inbound-synchronization-tutorial/IC750995.png "Indstillinger og Administratoroplysninger")    

    1.  I tekstfeltet **arbejdsdag administrator brugernavn** skal du skrive navnet på den bruger, du har oprettet i afsnittet [oprette en integration systembruger](https://msdn.microsoft.com/library/azure/Dn762434.aspx#BKMK_CreateUser) .    
    2.  I tekstfeltet **arbejdsdag administratoradgangskode** skal du skrive adgangskoden for den bruger, du har oprettet i afsnittet [oprette en integration systembruger](https://msdn.microsoft.com/library/azure/Dn762434.aspx#BKMK_CreateUser) .    
    3.  I tekstfeltet **arbejdsdag lejer URL-adresse** skal du skrive URL-adressen eller din arbejdsdag lejer.    

3.  Klik på **Start test** for at bekræfte forbindelse på siden **teste forbindelsen** , og klik derefter på **Næste**.    

    ![Test forbindelse] (./media/active-directory-saas-inbound-synchronization-tutorial/IC750996.png "Test forbindelse")  

4.  Klik på **Næste**på siden **Indstillinger for artikel Provisioning** .    

    ![Indstillinger for klargøring] (./media/active-directory-saas-inbound-synchronization-tutorial/IC750997.png "Indstillinger for klargøring")  

5.  Dialogboksen **Start klargøring** , klik på **udført**.    

    ![Start klargøring] (./media/active-directory-saas-inbound-synchronization-tutorial/IC750998.png "Start klargøring")  

Nu kan du gå til afsnittet **brugere** og kontrollere, om din arbejdsdag bruger er blevet importeret.    

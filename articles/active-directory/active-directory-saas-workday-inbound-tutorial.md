<properties 
    pageTitle="Selvstudium: Konfiguration af arbejdsdag til indgående synkronisering | Microsoft Azure" 
    description="Lær at bruge arbejdsdag som datakilde identitet til Azure Active Directory." 
    services="active-directory" 
    authors="MarkusVi"  
    documentationCenter="na" 
    manager="femila"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="10/10/2016" 
    ms.author="markvi" />


#<a name="tutorial-configuring-workday-for-inbound-synchronization"></a>Selvstudium: Konfiguration af arbejdsdag til indgående synkronisering


Formålet med dette selvstudium er så du kan se de trin, du skal udføre i arbejdsdag og Azure AD for at importere personer fra arbejdsdag til Azure AD. 

Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure AD Premium-abonnement
-   En lejer i arbejdsdag
  
Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1. Aktivere programmet integrationen for arbejdsdag 


2. Oprette en integration af bruger 


3. Oprette en sikkerhedsgruppe 


4. Tildele integration systembrugeren til sikkerhedsgruppen 


5. Konfigurere sikkerhedsindstillinger for gruppe 


6. Aktivering af sikkerhed politikændringer 


7. Import af konfiguration af brugers i Azure AD 



##<a name="enabling-the-application-integration-for-workday"></a>Aktivere programmet integrationen for arbejdsdag
  
Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integrationen for arbejdsdag.

### <a name="steps"></a>Trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-workday-inbound-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-workday-inbound-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj** i bunden af siden.

    ![Tilføj program] (./media/active-directory-saas-workday-inbound-tutorial/IC749321.png "Tilføj program")

  
5. Skriv **arbejdsdag**i søgefeltet.

    ![Tilføj et program fra gallerry] (./media/active-directory-saas-workday-inbound-tutorial/IC701021.png "Tilføj et program fra gallerry")

6. Vælg arbejdsdag i resultatruden, og klik derefter på udført for at tilføje programmet.

    ![Galleri med programmet] (./media/active-directory-saas-workday-inbound-tutorial/IC701022.png "Galleri med programmet")





## <a name="creating-an-integration-system-user"></a>Oprette en integration af bruger

### <a name="steps"></a>Trin:


1. Angiv i **Arbejdsdag Workbench**brugeroprettelsen i søgefeltet, og klik derefter på **Opret Integration systembruger**. 

    ![Opret bruger] (./media/active-directory-saas-workday-inbound-tutorial/IC750979.png "Opret bruger")



2. Fuldføre opgaven **Opret Integration systembruger** ved at angive et brugernavn og adgangskode til en ny Integration systembruger.  Lad kræver ny adgangskode ved næste Log på indstilling ikke er markeret, fordi denne bruger vil være logge på ved hjælp af programmering. Du kan lade Session Timeout minutter med standardværdien 0, hvilket forhindrer, at brugerens sessioner timeout for tidligt. 

    ![Oprette Integration af bruger] (./media/active-directory-saas-workday-inbound-tutorial/IC750980.png "Oprette Integration af bruger")
 




## <a name="creating-a-security-group"></a>Oprette en sikkerhedsgruppe

For dette scenario, der er beskrevet i dette selvstudium, skal du oprette en sikkerhedsgruppe begrænsninger integration system og tildele brugeren til den.

### <a name="steps"></a>Trin:

1. Angiv oprette sikkerhedsgruppen i søgefeltet, og klik derefter på **Opret sikkerhedsgruppe**. 

    ![CreateSecurity gruppe] (./media/active-directory-saas-workday-inbound-tutorial/IC750981.png "CreateSecurity gruppe")
 

2. Fuldføre opgaven oprette sikkerhedsgruppe.  Vælg Integration System-sikkerhedsgruppe – ugenert rullelisten Type af lejet sikkerhedsgruppe til at oprette en sikkerhedsgruppe, som medlemmer eksplicit tilføjes. 

    ![CreateSecurity gruppe] (./media/active-directory-saas-workday-inbound-tutorial/IC750982.png "CreateSecurity gruppe")
 



## <a name="assigning-the-integration-system-user-to-the-security-group"></a>Tildele integration systembrugeren til sikkerhedsgruppen

### <a name="steps"></a>Trin:


1. Angiv Rediger sikkerhedsgruppe i søgefeltet, og klik derefter på **Rediger sikkerhedsgruppe**. 

    ![Rediger sikkerhedsgruppe] (./media/active-directory-saas-workday-inbound-tutorial/IC750983.png "Rediger sikkerhedsgruppe")
 
 

2. Søge efter, og vælg den nye integration sikkerhedsgruppe ved navn. 

    ![Rediger sikkerhedsgruppe] (./media/active-directory-saas-workday-inbound-tutorial/IC750984.png "Rediger sikkerhedsgruppe")

 

3. Føje den nye integration systembruger til den nye sikkerhedsgruppe. 

    ![System sikkerhedsgruppe] (./media/active-directory-saas-workday-inbound-tutorial/IC750985.png "System sikkerhedsgruppe")  




## <a name="configuring-security-group-options"></a>Konfigurere sikkerhedsindstillinger for gruppe

I dette trin skal give du til de nye gruppetilladelser for **få** og **placere** handlinger på de objekter, der er sikret af følgende sikkerhedspolitikker til domæne:

- Klargøring af eksterne konto

- Arbejder Data: Offentlige arbejder rapporter

- Arbejder Data: Alle placeringer

- Arbejder Data: Aktuelle personale oplysninger

- Arbejder Data: Business titel på arbejderprofil

 
### <a name="steps"></a>Trin:

1. Angiv domæne sikkerhedspolitikker i søgefeltet, og derefter klikke på linket, domæne sikkerhedspolitikker for funktionsområder.  

    ![Domæne sikkerhedspolitikker] (./media/active-directory-saas-workday-inbound-tutorial/IC750986.png "Domæne sikkerhedspolitikker")  
 

2. Søg efter system, og vælg **System** funktionsområder.  Klik på **OK**.  

    ![Domæne sikkerhedspolitikker] (./media/active-directory-saas-workday-inbound-tutorial/IC750987.png "Domæne sikkerhedspolitikker")  


3. Udvid Administration af sikkerhed på listen over sikkerhedspolitikker for området System funktionelle, og vælg Domænesikkerhedspolitik, eksterne klargøring af konto.  

    ![Domæne sikkerhedspolitikker] (./media/active-directory-saas-workday-inbound-tutorial/IC750988.png "Domæne sikkerhedspolitikker")  


4. Klik på Rediger **Tilladelser**, og Tilføj derefter den nye sikkerhedsgruppe til listen over sikkerhedsgrupper med **få** og **placere** integration tilladelser på siden **Rediger tilladelser**dialogboksen. 

    ![Redigere tilladelser] (./media/active-directory-saas-workday-inbound-tutorial/IC750989.png "Redigere tilladelser")  

 

5. Gentag trin 1, ovenfor, at vende tilbage til skærmbilledet for at vælge funktionsområder, og denne gang skal søge efter personale, Vælg Staffing funktionsområder, og klik på **OK**.

    ![Domæne sikkerhedspolitikker] (./media/active-directory-saas-workday-inbound-tutorial/IC750990.png "Domæne sikkerhedspolitikker")  
 

6. På listen over sikkerhedspolitikker for området Staffing funktionelle, udvid arbejder Data: Staffing, og Gentag trin 4 ovenfor for hver af disse resterende sikkerhedspolitikker:

     - Arbejder Data: Offentlige arbejder rapporter

     - Arbejder Data: Alle placeringer

     - Arbejder Data: Aktuelle personale oplysninger

     - Arbejder Data: Business titel på arbejderprofil


    ![Domæne sikkerhedspolitikker] (./media/active-directory-saas-workday-inbound-tutorial/IC750991.png "Domæne sikkerhedspolitikker")  







## <a name="activating-security-policy-changes"></a>Aktivering af sikkerhed politikændringer

### <a name="steps"></a>Trin:

1. Angiv aktivere, i søgefeltet, og klik derefter på linket, aktivere ventende sikkerhed politikændringer. 

    ![Aktivere] (./media/active-directory-saas-workday-inbound-tutorial/IC750992.png "Aktivere") 
 

2. Begynde at aktivere ventende sikkerhed politikændringer opgaven ved at skrive en kommentar til overvågning, og klik derefter på **OK**. 

    ![Aktivere afventer sikkerhed] (./media/active-directory-saas-workday-inbound-tutorial/IC750993.png "Aktivere afventer sikkerhed")   
 

3. Fuldføre opgaven på det næste skærmbillede ved at markere afkrydsningsfeltet mærket Bekræft, og klik derefter på **OK**. 

    ![Aktivere afventer sikkerhed] (./media/active-directory-saas-workday-inbound-tutorial/IC750994.png "Aktivere afventer sikkerhed")  





## <a name="configuring-user-import-in-azure-ad"></a>Import af konfiguration af brugers i Azure AD

Formålet med dette afsnit er at oprette en disposition for at konfigurere Azure AD for at importere personer fra arbejdsdag.


### <a name="steps"></a>Trin:


1. Klik på **Konfigurer bruger importere** for at åbne dialogboksen **Konfigurer klargøring** på siden **arbejdsdag** application integration.


2. Udfør følgende trin på siden **Indstillinger og Administratoroplysninger** , og klik derefter på **Næste**: 

    ![Indstillinger og Administratoroplysninger] (./media/active-directory-saas-workday-inbound-tutorial/IC750995.png "Indstillinger og Administratoroplysninger")  
 
    en. I arbejdsdag administrator bruger tekstfeltet skal du skrive navnet på den bruger, du har oprettet i siden for oprettelse af en integration system bruger sektion.

    b. I tekstfeltet arbejdsdag administrator adgangskode skal du skrive adgangskoden for den bruger, du har oprettet i siden for oprettelse af en integration system bruger sektion.

    c. I tekstfeltet arbejdsdag lejer URL-adresse skal du skrive URL-adressen eller din arbejdsdag lejer.


3. Klik på **Start test** for at bekræfte forbindelse på siden **teste forbindelsen** , og klik derefter på **Næste**. 

    ![Test forbindelse] (./media/active-directory-saas-workday-inbound-tutorial/IC750996.png "Test forbindelse")  
 

4. Klik på **Næste**på siden **artikel Provisioning** indstillinger. 

    ![Indstillinger for klargøring] (./media/active-directory-saas-workday-inbound-tutorial/IC750997.png "Indstillinger for klargøring")


5. Dialogboksen **Start klargøring** , klik på **udført**. 

    ![Start klargøring] (./media/active-directory-saas-workday-inbound-tutorial/IC750998.png "Start klargøring")
 

Nu kan du gå til afsnittet **brugere** og kontrollere, om din arbejdsdag bruger er blevet importeret.



## <a name="additional-resources"></a>Yderligere ressourcer

* [Liste over selvstudier om, hvordan du integrerer SaaS Apps med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Hvad er adgang til programmer og single sign-on med Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<properties 
    pageTitle="Oversigt over integration konti og Enterprise-Integration Pack | Microsoft Azure App Service | Microsoft Azure" 
    description="Få mere at vide om Integration konti alle, Enterprise Integration Pack og logik apps" 
    services="logic-apps" 
    documentationCenter=".net,nodejs,java"
    authors="msftman" 
    manager="erikre" 
    editor="cgronlun"/>

<tags 
    ms.service="logic-apps" 
    ms.workload="integration" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="07/08/2016" 
    ms.author="deonhe"/>

# <a name="overview-of-integration-accounts"></a>Oversigt over integration af konti

## <a name="what-is-an-integration-account"></a>Hvad er en integration-konto?
En integration konto er en Azure-konto, som muliggør virksomhedsintegration apps til at administrere elementer, herunder skemaer, kort, certifikater, partnere og aftaler. En integration-app, du opretter skal bruge en integration konto for at få adgang til et skema, kort eller certifikatet, f.eks.

## <a name="create-an-integration-account"></a>Oprette en integration-konto 
1. Vælg **Gennemse**   
![](./media/app-service-logic-enterprise-integration-accounts/account-1.png)  
2. Angiv **integration** i søgefeltet filter, og vælg **Integration konti** fra listen med søgeresultater     
 ![](./media/app-service-logic-enterprise-integration-accounts/account-2.png)  
3. Vælg knappen *Tilføj* i menuen i toppen af siden      
![](./media/app-service-logic-enterprise-integration-accounts/account-3.png)  
4. Skriv **navnet**, Vælg det **abonnement** , du vil bruge, enten oprette en ny **ressourcegruppe** eller Vælg en eksisterende ressourcegruppe skal du vælge en **placering** hvor kontoen integration skal placeres, Vælg en **priser niveau**, og vælg derefter knappen **Opret** .   

  På dette tidspunkt vil kontoen integration klargøres i den valgte placering. Dette bør fuldført inden for 1 minut.    
![](./media/app-service-logic-enterprise-integration-accounts/account-4.png)  
5. Opdatere siden. Du får vist din nye integration-konto, der er angivet. Tillykke!  
![](./media/app-service-logic-enterprise-integration-accounts/account-5.png) 

## <a name="how-to-link-an-integration-account-to-a-logic-app"></a>Hvordan du kan sammenkæde et firma integration med en logik app
I rækkefølge for dine logik apps til at få adgang til kort, skemaer, aftaler og andre elementer, der findes i din integration-konto, skal du først sammenkæde kontoen integration med din logik app.

### <a name="here-are-the-steps-to-link-an-integration-account-to-a-logic-app"></a>Her er trinnene for at sammenkæde et firma integration med en logik app 

#### <a name="prerequisites"></a>Forudsætninger
- En integration-konto
- En logik app

>[AZURE.NOTE]Kontrollér, at din integration konto og logik app er i den **samme Azure placering** , inden du går i gang

1. Vælg linket **Indstillinger** i menuen af din app logik  
![](./media/app-service-logic-enterprise-integration-accounts/linkaccount-1.png)   
2. Vælg **Integration konto** elementet bladet indstillinger  
![](./media/app-service-logic-enterprise-integration-accounts/linkaccount-2.png)   
3. Vælg den integration-konto, du vil oprette et link til din app, **Vælg en Integration konto** rulleliste logik  
![](./media/app-service-logic-enterprise-integration-accounts/linkaccount-3.png)   
4. Gem dit arbejde  
![](./media/app-service-logic-enterprise-integration-accounts/linkaccount-4.png)   
5. Du får vist en meddelelse, der angiver, at kontoen integration der er knyttet til din logik app, og at alle elementer i kontoen integration nu er tilgængelige for din logik app.  
![](./media/app-service-logic-enterprise-integration-accounts/linkaccount-5.png)   

Nu, hvor kontoen integration er knyttet til din logik app, kan du gå til din logik app og bruge B2B forbindelser som XML-validering, flad fil/dekodning eller Transformer til at oprette apps med B2B funktioner.  
    
## <a name="how-to-delete-an-integration-account"></a>Sådan sletter du en integration-konto?
1. Vælg **Gennemse**  
![](./media/app-service-logic-enterprise-integration-overview/overview-1.png)    
2. Indtast **integration** i feltet filter Søg og vælg **Integration konti** på listen med søgeresultater     
 ![](./media/app-service-logic-enterprise-integration-overview/overview-2.png)  
3. Markér den **integration konto** , du vil slette  
![](./media/app-service-logic-enterprise-integration-overview/overview-3.png)  
4. Vælg linket **Slet** , der er placeret i menuen   
![](./media/app-service-logic-enterprise-integration-accounts/delete.png)  
5. Bekræfte dit valg    

## <a name="how-to-move-an-integration-account"></a>Sådan flytter du en integration-konto?
Du kan nemt flytte en integration konto til et nyt abonnement og en ny ressourcegruppe. Følg disse trin, hvis du vil flytte kontoen integration:

>[AZURE.IMPORTANT] Du skal opdatere alle scripts for at bruge den nye ressource id'er, når du flytter en integration-konto.

1. Vælg **Gennemse**  
![](./media/app-service-logic-enterprise-integration-overview/overview-1.png)    
2. Indtast **integration** i feltet filter Søg og vælg **Integration konti** på listen med søgeresultater     
 ![](./media/app-service-logic-enterprise-integration-overview/overview-2.png)  
3. Markér den **integration konto** , du vil slette  
![](./media/app-service-logic-enterprise-integration-overview/overview-3.png)  
4. Vælg linket **flytte** , der er placeret i menuen   
![](./media/app-service-logic-enterprise-integration-accounts/move.png)  
5. Bekræfte dit valg    

## <a name="next-steps"></a>Næste trin
- [Lær mere om Enterprise Integration Pack] (./app-service-logic-enterprise-integration-overview.md "Få mere at vide om Enterprise Integration Pack")  
- [Lær mere om aftaler] (./app-service-logic-enterprise-integration-agreements.md "Få mere at vide om enterprise integration aftaler")  


 
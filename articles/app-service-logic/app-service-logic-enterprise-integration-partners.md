<properties 
    pageTitle="Få mere at vide om partnere og Enterprise-Integration Pack | Microsoft Azure App Service | Microsoft Azure" 
    description="Lær at bruge partnere til Enterprise-Integration Pack og logik-apps" 
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

# <a name="learn-about-partners-and-enterprise-integration-pack"></a>Få mere at vide om partnere og Enterprise-Integration Pack

## <a name="overview"></a>Oversigt
Før du kan oprette en partner, dele du og den organisation, du har til hensigt at gøre forretninger med oplysninger, der hjælper dig med at identificere såvel validere meddelelser, der sendes fra hinanden. Når du har disse diskussion, og du er klar til at starte dine forretningsmæssige relationer, kan du for at oprette en *partner* i kontoen integration.

## <a name="what-is-a-partner"></a>Hvad er en partner?
Partnere er de enheder, deltager i Business To Business (B2B) messaging og transaktioner. 

## <a name="how-are-partners-used"></a>Sådan bruges partnere?
Partnere, der bruges til at oprette aftaler. En aftale, der definerer oplysninger om de meddelelser, der vil blive udvekslet mellem partnere. 

Før du kan oprette en aftale, skal du har tilføjet mindst to partnere til kontoen integration. En af partnerne til en aftale skal være din organisation. Den partner, som repræsenterer organisationen kaldes **host partner**. Den anden partner skal repræsentere andre organisationen, som din organisation udveksler meddelelser. Den anden partner kendes som **Gæst partner**. Gæst partner kan være et andet firma eller endda en afdeling i din egen organisation.  

Når du har tilføjet partnerne, skal du bruge disse partnere til at oprette en aftale. 

Modtage og sende indstillingerne er et overblik fra synspunkt hostet Partner. For eksempel Modtag indstillingerne i en aftale finde ud af, hvordan hostet partner modtager meddelelser, der sendes fra en gæst partner. Send indstillingerne i aftalen angiver på samme måde, hvordan hostet partner sender meddelelser til gæst partner.

## <a name="how-to-create-a-partner"></a>Sådan oprettes en partner?
Fra Azure-portalen:  
1. Vælg **Gennemse**  
![](./media/app-service-logic-enterprise-integration-overview/overview-1.png)    
2. Indtast **integration** i feltet filter Søg og vælg **Integration konti** på listen med søgeresultater     
 ![](./media/app-service-logic-enterprise-integration-overview/overview-2.png)  
3. Vælg den **integration konto** , hvor du vil tilføje partnerne  
![](./media/app-service-logic-enterprise-integration-overview/overview-3.png)  
4.  Vælg feltet **partnere**  
![](./media/app-service-logic-enterprise-integration-partners/partner-1.png)  
5. Vælg knappen **Tilføj** i bladet partnere, der åbnes  
![](./media/app-service-logic-enterprise-integration-partners/partner-2.png)  
6. Angiv et **navn** til din partner, og derefter vælge **operatoren **, til sidst skal du angive en **værdi**. Værdien, der bruges til at hjælpe med at identificere dokumenter, der kommer ind i dine apps.  
![](./media/app-service-logic-enterprise-integration-partners/partner-3.png)  
7. Vælg *klokkeikonet besked om at få vist status for oprettelsen af partner* .  
![](./media/app-service-logic-enterprise-integration-partners/partner-4.png)  
8. Vælg feltet **partnere** . Dette opdaterer feltet, og du bør se antallet af partnere stigning, er afspejler den nye partner blevet tilføjet.    
![](./media/app-service-logic-enterprise-integration-partners/partner-5.png)  
10. Når du markerer feltet partnere, kan du også se nyligt tilføjede partner vises i bladet partnere.    
![](./media/app-service-logic-enterprise-integration-partners/partner-6.png)  

## <a name="how-to-edit-a-partner"></a>Sådan redigeres en partner

Følg disse trin for at redigere en partner, der allerede findes i kontoen integration:  
1. Vælg feltet **partnere**  
2. Vælg den partner, du vil redigere, når bladet partnere åbnes  
3. Foretag de ændringer, skal du under feltet **Opdater Partner**  
4. Hvis du er tilfreds med ændringerne, vælge **Gem** linket, ellers skal du vælge linket **Slet** ikke længere væk ændringerne at.  
![](./media/app-service-logic-enterprise-integration-partners/edit-1.png)  

## <a name="how-to-delete-a-partner"></a>Sådan sletter du en partner
1. Vælg feltet **partnere**  
2. Vælg den partner, du vil redigere, når bladet partnere åbnes  
3. Vælg **Slet** link    
![](./media/app-service-logic-enterprise-integration-partners/delete-1.png)   

## <a name="next-steps"></a>Næste trin
- [Lær mere om aftaler] (./app-service-logic-enterprise-integration-agreements.md "Få mere at vide om enterprise integration aftaler")  


